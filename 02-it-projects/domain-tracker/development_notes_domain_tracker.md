# Development Notes (Domain Tracker)

## List of Values

- DNS Managers

```json
[
  {
    "label": "Namecheap",
    "value": "Namecheap"
  },
  {
    "label": "DigitalOcean",
    "value": "DigitalOcean"
  },
  {
    "label": "Cloudflare",
    "value": "Cloudflare"
  },
  {
    "label": "GreenGeeks",
    "value": "GreenGeeks"
  },
  {
    "label": "Others",
    "value": "Others"
  }
]
```

- Domain Status

```json
[
  {
    "label": "Active",
    "value": "Active"
  },
  {
    "label": "Inactive",
    "value": "Inactive"
  },
  {
    "label": "Expired",
    "value": "Expired"
  }
]
```

DEFAULT 'Namecheap',
`status` enum('Active','Pending','Inactive','Maintenance','Decommissioned') NOT NULL DEFAULT 'Active',
`project` enum('MedProjects','HealthSpace','Kranium','QC HIS','Others') NOT NULL,
`cloud_provider` enum('DigitalOcean','Azure','GreenGeeks','Others') NOT NULL DEFAULT 'DigitalOcean',

- Subdomain Status

```
[
  {
    "label": "Active",
    "value": "Active"
  },
  {
    "label": "Inactive",
    "value": "Inactive"
  },
  {
    "label": "Decommissioned",
    "value": "Decommissioned"
  }
]
```

- Projects

```
[
  {
    "label": "MedProjects",
    "value": "MedProjects"
  },
  {
    "label": "HealthSpace",
    "value": "HealthSpace"
  },
  {
    "label": "Kranium",
    "value": "Kranium"
  },
  {
    "label": "QC HIS",
    "value": "QC HIS"
  },
  {
    "label": "Others",
    "value": "Others"
  }
]
```

- Cloud Provider (Where Servers Are)

```
[
  {
    "label": "DigitalOcean",
    "value": "DigitalOcean"
  },
  {
    "label": "GreenGeeks",
    "value": "GreenGeeks"
  },
  {
    "label": "Azure",
    "value": "Azure"
  },
  {
    "label": "Others",
    "value": "Others"
  }
]
```

## MySQL Queries

- SELECT domains

  ````mysql
  SELECT id, domain_name FROM domains ORDER BY domain_name ASC;
  ````

- SELECT subdomains

```mysql
SELECT
    s.id,
    s.subdomain_name,
    d.domain_name,
    CONCAT(s.subdomain_name, '.', d.domain_name) AS full_domain,
    s.dns_manager,
    s.status,
    s.project,
    s.cloud_provider,
    s.ip_address,
    s.remarks,
    s.tasks,
    s.created_at,
    s.updated_at
FROM
    subdomains s
JOIN
    domains d ON s.domain_id = d.id
{{ domain_dropdown.selectedOptionValue ? "WHERE s.domain_id = " + domain_dropdown.selectedOptionValue : "" }}
```

- INSERT subdomain

```mysql
INSERT INTO `subdomains` (
  `domain_id`,
  `subdomain_name`,
  `dns_manager`,
  `status`,
  `project`,
  `cloud_provider`,
  `ip_address`,
  `remarks`,
  `tasks`,
  `created_at`,
  `updated_at`
) VALUES (
  {{domainId}},
  {{subdomainName}},
  {{dnsManager}},
  {{status}},
  {{project}},
  {{cloudProvider}},
  {{ipAddress}},
  {{remarks}},
  {{tasks}},
  NOW(),
  NOW()
);
```

- UPDATE subdomains

```mysql
UPDATE `subdomains` SET
  `domain_id` = {{domainId}},
  `subdomain_name` = {{subdomainName}},
  `dns_manager` = {{dnsManager}},
  `status` = {{status}},
  `project` = {{project}},
  `cloud_provider` = {{cloudProvider}},
  `ip_address` = {{ipAddress}},
  `remarks` = {{remarks}},
  `tasks` = {{tasks}},
  `updated_at` = NOW()
WHERE `id` = {{subdomainId}};
```

- DELETE subdomain

```mysql
DELETE FROM `subdomains` WHERE `id` = {{subdomainId}};
```

