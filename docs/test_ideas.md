ref : done with GPT

## 1. Basic Functionality Test

**Objective**: Ensure that Pipex correctly handles simple commands and file redirections.

**Test Case**:

```bash
./pipex infile.txt "grep 'pattern'" "wc -l" outfile.txt
```

**Expected Behavior**:

- The content of `infile.txt` is passed to `grep 'pattern'`.
- The output of the `grep` command is then passed to `wc -l`.
- The final result (number of lines containing 'pattern') is written to `outfile.txt`.

**Verification**:

- Compare the content of `outfile.txt` with the expected result.
- Ensure no errors are reported during execution.

## 2. Handling Non-Existent Input File

**Objective**: Verify that Pipex gracefully handles cases where the input file does not exist.

**Test Case**:

```bash
./pipex non_existent_file.txt "ls -l" "wc -l" output.txt
```

**Expected Behavior**:

- Pipex should display an error message indicating that the input file cannot be found or opened.
- `output.txt` should not be created or should be empty.

**Verification**:

- Check for the appropriate error message.
- Ensure `output.txt` is not created or remains empty.

## 3. Command Not Found

**Objective**: Test Pipex's response when a specified command is invalid or not found.

**Test Case**:

```bash
./pipex infile.txt "invalid_command" "wc -l" output.txt
```

**Expected Behavior**:

- Pipex should display an error message indicating that `invalid_command` is not found.
- The program should handle the error gracefully without crashing.

**Verification**:

- Observe the error message for clarity and accuracy.
- Ensure the program exits gracefully.

## 4. Permission Denied on Output File

**Objective**: Check how Pipex handles situations where it lacks write permissions to the output file.

**Test Case**:

```bash
touch restricted_output.txt
chmod 444 restricted_output.txt  # Read-only permissions
./pipex infile.txt "ls -l" "wc -l" restricted_output.txt
```

**Expected Behavior**:

- Pipex should display an error message indicating that it cannot write to `restricted_output.txt` due to insufficient permissions.

**Verification**:

- Confirm the presence of the appropriate error message.
- Ensure no unintended modifications to `restricted_output.txt`.

## 5. Multiple Commands (Bonus)

**Objective**: Verify that Pipex can handle multiple commands in a pipeline, if implemented.

**Test Case**:

```bash
./pipex infile.txt "grep 'pattern'" "sort" "uniq -c" "sort -nr" outfile.txt
```

**Expected Behavior**:

- The content of `infile.txt` is processed through the series of commands.
- The final sorted and counted result is written to `outfile.txt`.

**Verification**:

- Compare `outfile.txt` with the expected output.
- Ensure the pipeline processes data correctly through all stages.
