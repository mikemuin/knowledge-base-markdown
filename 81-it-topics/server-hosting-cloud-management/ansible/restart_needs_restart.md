# Ansible Playbook Explanation: Server Restart Management

## What This Playbook Does

This Ansible playbook automatically checks all Ubuntu servers in your inventory to see if they need to be restarted (typically after system updates), and then safely restarts only those servers that actually require it.

## Playbook Structure Breakdown

### 1. Playbook Header and Metadata

```yaml
---
- name: Restart only servers that require reboot
  hosts: all
  become: yes
  gather_facts: yes
  strategy: linear
  serial: 1
```

**Explanation:**

- `---`: YAML document start marker
- `name`: Human-readable description of what this playbook does
- `hosts: all`: Targets all servers listed in your inventory file
- `become: yes`: Runs tasks with elevated privileges (sudo)
- `gather_facts: yes`: Collects system information about each server (OS, memory, etc.)
- `strategy: linear`: Executes tasks on hosts in order (default behavior)
- `serial: 1`: Processes one server at a time (prevents multiple simultaneous reboots)

### 2. Variables Section

```yaml
vars:
  confirmation_prompt: true
  pre_restart_delay: 10
  post_restart_wait: 60
```

**Explanation:**

- `vars`: Defines variables that can be used throughout the playbook
- `confirmation_prompt`: Boolean that controls whether to ask for user confirmation
- `pre_restart_delay`: Seconds to wait before actually restarting
- `post_restart_wait`: Seconds to wait after reboot before checking if server is back

### 3. Pre-Tasks Section

```yaml
pre_tasks:
  - name: Safety warning for running against all hosts
    debug:
      msg: "WARNING: This playbook will check ALL hosts in inventory for reboot requirements"
```

**Explanation:**

- `pre_tasks`: Runs before the main tasks
- `debug`: Ansible module that prints messages to the console
- This serves as a safety reminder that the playbook affects all servers

### 4. Main Tasks Section

#### Task 1: Check Reboot Requirement

```yaml
- name: Check if reboot is required on Ubuntu
  stat:
    path: /var/run/reboot-required
  register: reboot_required
```

**Explanation:**

- `stat`: Ansible module that checks if a file/directory exists
- `/var/run/reboot-required`: Ubuntu creates this file when a reboot is needed
- `register`: Stores the result in a variable called `reboot_required`

#### Task 2: Gather Reboot Requirements

```yaml
- name: Gather reboot requirements across all hosts
  set_fact:
    servers_needing_reboot: "{{ play_hosts | select('in', hostvars.keys()) | select('extract', hostvars, 'reboot_required') | select('extract', hostvars, 'reboot_required', 'stat', 'exists') | list }}"
  run_once: true
```

**Explanation:**

- `set_fact`: Creates a new variable with computed data
- `play_hosts`: List of all hosts in the current play
- `hostvars`: Contains variables for all hosts
- `run_once: true`: Only executes this task once, not on every host
- The complex filter creates a list of servers that need rebooting

#### Task 3: Log When No Reboot Needed

```yaml
- name: Log when no reboot is required
  debug:
    msg: "No reboot required on {{ inventory_hostname }}"
  when: not reboot_required.stat.exists
```

**Explanation:**

- `{{ inventory_hostname }}`: Variable containing the current server's name
- `when`: Conditional that only runs this task if the condition is true
- `not reboot_required.stat.exists`: Only runs if the reboot file doesn't exist

#### Task 4: Display Servers Needing Reboot

```yaml
- name: Display servers that need reboot
  debug:
    msg: "{{ inventory_hostname }} requires a reboot"
  when: reboot_required.stat.exists
```

**Explanation:**

- Similar to previous task but runs when reboot IS required
- Helps identify which servers will be restarted

#### Task 5: User Confirmation

```yaml
- name: Confirm server restart
  pause:
    prompt: "Are you sure you want to restart servers that require reboot? (yes/no)"
  register: confirmation
  when: confirmation_prompt | bool and servers_needing_reboot | length > 0
  run_once: true
  serial: 1
```

**Explanation:**

- `pause`: Stops execution and waits for user input
- `register: confirmation`: Stores user input in a variable
- `when`: Only runs if prompts are enabled AND there are servers to restart
- `| bool`: Converts the variable to boolean type
- `| length > 0`: Checks if the list has any items

#### Task 6: Exit if Not Confirmed

```yaml
- name: Exit playbook if not confirmed
  meta: end_play
  when: confirmation_prompt | bool and confirmation.user_input is defined and confirmation.user_input != "yes"
```

**Explanation:**

- `meta: end_play`: Stops the entire playbook execution
- `is defined`: Checks if the variable exists
- `!= "yes"`: Stops unless user typed exactly "yes"

#### Task 7: Restart Block

```yaml
- when: reboot_required.stat.exists
  block:
```

**Explanation:**

- `when`: Only runs the entire block if reboot is required
- `block`: Groups multiple tasks together that share the same condition

##### Subtask: Check Uptime Before

```yaml
- name: Check uptime before restart
  command: uptime
  register: uptime_before
  changed_when: false
```

**Explanation:**

- `command`: Runs a shell command
- `uptime`: Linux command showing how long the system has been running
- `changed_when: false`: Tells Ansible this command doesn't change system state

##### Subtask: Display Uptime

```yaml
- name: Display server uptime
  debug:
    var: uptime_before.stdout
```

**Explanation:**

- `var`: Shows the contents of a variable
- `stdout`: The text output from the previous command

##### Subtask: Notify About Restart

```yaml
- name: Notify about upcoming restart
  debug:
    msg: "Reboot required on {{ inventory_hostname }}! System will restart in {{ pre_restart_delay }} seconds..."
```

**Explanation:**

- Gives advance warning before the restart happens
- Uses variables to show the server name and delay time

##### Subtask: Wait Before Restart

```yaml
- name: Wait before restart
  pause:
    seconds: "{{ pre_restart_delay }}"
```

**Explanation:**

- `pause`: Stops execution for a specified time
- `seconds`: How long to wait (uses the variable defined earlier)

##### Subtask: Actually Restart

```yaml
- name: Restart the server
  shell: sleep 2 && shutdown -r now "Ansible triggered reboot"
  async: 30
  poll: 0
```

**Explanation:**

- `shell`: Runs a shell command (more powerful than `command`)
- `sleep 2`: Waits 2 seconds to ensure the command completes
- `shutdown -r now`: Linux command to restart immediately
- `async: 30`: Runs in background, timeout after 30 seconds
- `poll: 0`: Don't wait for completion (fire and forget)

##### Subtask: Wait for Restart to Complete

```yaml
- name: Wait for server to complete restart
  pause:
    seconds: "{{ post_restart_wait }}"
```

**Explanation:**

- Gives the server time to fully shut down and start back up
- Uses the variable defined at the top

##### Subtask: Wait for Connection

```yaml
- name: Wait for server to become reachable
  wait_for_connection:
    delay: 10
    timeout: 300
```

**Explanation:**

- `wait_for_connection`: Ansible module that waits for SSH to be available
- `delay: 10`: Wait 10 seconds before first connection attempt
- `timeout: 300`: Give up after 5 minutes if no connection

##### Subtask: Check New Uptime

```yaml
- name: Check uptime after restart
  command: uptime
  register: uptime_after
  changed_when: false
```

**Explanation:**

- Same as the earlier uptime check
- Proves the server actually restarted (uptime should be very low)

##### Subtask: Verify System Health

```yaml
- name: Verify server status
  command: systemctl --failed --no-pager
  register: failed_services
  changed_when: false
```

**Explanation:**

- `systemctl --failed`: Lists any services that failed to start
- `--no-pager`: Prevents interactive pager from starting
- Helps identify if the restart caused any problems

##### Subtask: Report Issues

```yaml
- name: Report failed services (if any)
  debug:
    var: failed_services.stdout_lines
```

**Explanation:**

- `stdout_lines`: Shows command output as a list (one line per item)
- Displays any services that didn't start properly

##### Subtask: Success Message

```yaml
- name: Restart completed successfully
  debug:
    msg: "Server restart completed successfully on {{ inventory_hostname }}"
```

**Explanation:**

- Confirms the restart process completed without errors
- Shows which server was successfully restarted

## Key Ansible Concepts Demonstrated

### 1. Inventory Management

- Uses `hosts: all` to target all servers in your inventory file
- `inventory_hostname` variable contains each server's name

### 2. Privilege Escalation

- `become: yes` runs tasks with root privileges
- Necessary for system operations like rebooting

### 3. Fact Gathering

- `gather_facts: yes` collects system information
- Provides variables like OS type, memory, disk space, etc.

### 4. Variables and Templating

- Variables defined in `vars` section
- Jinja2 templating with `{{ variable_name }}` syntax
- `register` stores task results in variables

### 5. Conditional Logic

- `when` clauses control task execution
- Boolean logic and comparisons
- `is defined` checks variable existence

### 6. Control Flow

- `block` groups related tasks
- `meta: end_play` stops execution
- `run_once: true` executes task only once across all hosts

### 7. Modules Used

- `debug`: Display messages and variable contents
- `stat`: Check file/directory existence
- `set_fact`: Create computed variables
- `pause`: Wait for time or user input
- `command`/`shell`: Execute system commands
- `wait_for_connection`: Wait for SSH availability

### 8. Error Handling and Safety

- Confirmation prompts before destructive actions
- Verification steps after major changes
- `changed_when: false` for read-only operations

### 9. Asynchronous Operations

- `async` and `poll` for background tasks
- Necessary for operations that disconnect SSH (like reboots)

## How to Use This Playbook

1. **Create an inventory file** listing your servers
2. **Run the playbook**: `ansible-playbook -i inventory.yml restart_needs_restart.yml`
3. **Follow the prompts** to confirm restarts
4. **Monitor the output** to ensure all servers restart successfully

This playbook demonstrates advanced Ansible concepts while solving a real-world problem: safely managing server restarts across your infrastructure.