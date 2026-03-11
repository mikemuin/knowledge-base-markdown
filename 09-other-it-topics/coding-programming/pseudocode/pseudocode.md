## Pseudocode

Pseudocode is a written design notation that is not based on any programming language. It is not natural language like written English and it is not formal code used in high level languages.

Pseudocode allows a programmer to use indentation to clearly state the use of programming constructs such as iteration (loops) and selection statements (IF or CASE).

When creating pseudocode, it is important to remember:

- To define the steps of the main algorithm
- To refine the main steps where possible (not all main steps need refined)
- That there is no need to declare variables
- Indentation should be used to help identify use of loops and selection statements
- **Not to** write steps using a formal language such as Visual Basic, True Basic, LiveCode, Python or Reference Language

## Pseudocode example

The programmer will consider the purpose and functional requirements that were established at the analysis phase. In this example, the information from the analysis phase is shown below to provide a context for the pseudocode examples that follow.

### Analysis used to inform design

#### Purpose

- A program is to be developed to create usernames for a class of twenty pupils
- The program will ask a teacher to enter the first name, surname and age of each pupil.
- The age entered must be between five and eighteen.
- The program should output a list of usernames for the teacher.

#### Functional requirements

| Inputs           | Processes       | Outputs           |
| ---------------- | --------------- | ----------------- |
| Pupil first name | Validate age    | List of usernames |
| Pupil surname    | Create username |                   |
| Pupil age        |                 |                   |

### Design using pseudocode

#### Main steps (algorithim)

```
1 Initialise username
2 Start fixed loop for twenty pupils
3     Get first name and surname from user
4     Get valid age from user
5     Generate username
6 Display "Username", index, "is" username
7 End fixed loop
```

#### Refinement

```
3.1 Get first name and store in first name array
3.2 Get surname and store in surname array
4.1 Get age and store in age array
4.2 While age is less than 5 or higher than 18 start conditional loop
4.3     Display error message "Invalid age, enter a number between 5 and 18 inclusive"
4.4     Get age and store in age array
4.5 End conditional loop
5.1 Concatenate first name, surname and age and store in username array
5.2 Start fixed loop from 0 to index -1
5.3    If username equals stored username
5.4      Add 1 to age
5.5      Concatenate first name, surname and age and store in username array
5.6     End If
5.7 End fixed loop
```

In the above example, the algorithm is designed in such a way that if a pupil has the same username as one already stored, the code will add one to their age and attempt to store the username again. The loop will repeat until the username generated is unique.