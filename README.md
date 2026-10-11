
# Lab 5: Dynamic Testing — Vulnerability Research

## Project Overview

In this lab, I moved from static code analysis to dynamic security testing. My goal was to interact with a running application, examine how it handles requests, and determine whether potential security weaknesses identified during static analysis could be reproduced and have a measurable security impact.

Rather than assuming a vulnerability exists based on source code alone, I focused on gathering evidence through controlled testing.

## Step 1: Prepare the Dynamic Testing Environment

Before beginning dynamic testing, I needed to verify that I was working with the same application version and source code examined during Lab 4.

This was important because changes to the application's source code could affect my testing results and make it difficult to reproduce findings.

### Target Application Baseline

| Component | Details |
|---|---|
| Application | Calibre-Web NextGen |
| Version | 4.1.43 |
| Branch | `main` |
| Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` |
| Testing Method | Dynamic Application Security Testing |

### Verify Repository Integrity

I opened PowerShell in my vulnerability research lab directory and ran:

```powershell
git -C .\targets\Calibre-Web-NextGen status --short
```

**What this command does:**

The command checks whether any tracked files have been modified or deleted and whether untracked files exist in the target repository.

**Expected result:**

If PowerShell returns no output, the working tree is clean.

**Why this matters:**

Confirming a clean working tree helps establish a consistent testing environment. It reduces the risk of confusing local source-code changes with application behavior observed during dynamic testing.

This check verifies the working tree's cleanliness; the version and commit must also be checked separately to confirm the exact source baseline.

### Next Step

After verifying the repository baseline, I can proceed with preparing the running application for controlled security testing.
  

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/97bf0a3d69e8a973cc74209b3e5e690070ef3215/Screenshot%202026-09-17%20092356.png)


### Step 2: Verify the Exact Target Commit

After confirming that the Calibre-Web NextGen repository had no modified or untracked files, I needed to verify that I was working with the exact commit used during my Lab 4 static analysis.

I opened PowerShell and ran the following command:

```powershell
git -C .\targets\Calibre-Web-NextGen rev-parse HEAD
```

**What this command does:**

This command retrieves the full commit hash currently checked out in the target repository.

**Expected Result:**

```text
7e9221f455329bb3e6611b4652ac23b7f8629bb0
```

**Why this matters:**

Verifying the commit hash ensures that my dynamic testing uses the same source-code revision examined during static analysis.

If the returned commit matches my Lab 4 baseline, I can proceed knowing that both phases of testing reference the same code revision.

**Security Testing Significance:**

Maintaining a consistent source-code baseline improves reproducibility, strengthens the reliability of my evidence, and helps ensure that any observed security behavior can be traced back to the correct application revision.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/35a979ca0bc7bb66ea31c9a98eb464a94947fc68/Screenshot%202026-09-17%20092345.png)


### Step 3: Verify the Target Repository Branch

After confirming that the target repository was clean and the commit hash matched my Lab 4 baseline, I verified the active Git branch.

This step helped ensure that I was working from the expected branch before continuing with dynamic security testing.

**PowerShell Command:**

```powershell
git -C .\targets\Calibre-Web-NextGen branch --show-current
```

**What this command does:**

This command displays the name of the currently checked-out Git branch.

**Expected Result:**

```text
main
```

**Verification Results:**

| Verification Check | Result | Status |
|---|---|---|
| Target Application | Calibre-Web NextGen | Confirmed |
| Application Version | 4.1.43 | Baseline |
| Active Branch | `main` | Verified |
| Commit Hash | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` | Verified |
| Working Tree | Clean | Verified |

**Why this matters:**

By verifying the branch, commit hash, and repository integrity, I established a consistent source-code baseline for my dynamic testing.

This allows me to compare application behavior against the same code examined during Lab 4 and document any potential security findings with greater accuracy.

**Outcome:**

The Calibre-Web NextGen source repository matched my established Lab 4 baseline.

With the repository verification complete, I was ready to move into the next phase: preparing and running the application in a controlled testing environment.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/27a78aebd5813135ec1488ff1384d945ff61fbf0/Screenshot%202026-09-17%20092826.png)


### Step 4: Verify the Application Version

After verifying the repository's working tree, commit hash, and active branch, I checked the application's source code to confirm its version.

My goal was to ensure that the Calibre-Web NextGen source repository matched the version established during Lab 4 before continuing with dynamic testing.

**PowerShell Command:**

```powershell
Select-String -Path .\targets\Calibre-Web-NextGen\cps\constants.py -Pattern "STABLE_VERSION"
```

**What this command does:**

This PowerShell command searches the application's `constants.py` file for the `STABLE_VERSION` variable, which identifies the stable application version defined in the source code.

**Expected Result:**

```text
STABLE_VERSION = "4.1.43"
```

**Why this matters:**

Verifying the application version helps establish consistency between my static analysis and dynamic testing environments.

Testing a different application version could introduce changes to routes, authentication mechanisms, authorization checks, or other security controls, potentially affecting the reliability of my findings.

**Verification Summary:**

| Baseline Component | Expected Value | Status |
|---|---|---|
| Application | Calibre-Web NextGen | Confirmed |
| Git Branch | `main` | Verified |
| Commit Hash | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` | Verified |
| Working Tree | Clean | Verified |
| Application Version | `4.1.43` | Pending command verification |

**Outcome:**

The repository branch, commit, and working tree matched my Lab 4 baseline. The final source-code version check will confirm whether the application also identifies itself as version `4.1.43`.

Once confirmed, I can proceed with preparing the application for controlled dynamic security testing.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a4d3fa4f575f4a5548816fe36d0e89af1cba468e/Screenshot%202026-09-17%20093417.png)


### Step 5: Locate the Application Version in the Source Code

During my application version verification, I searched `constants.py` for the `STABLE_VERSION` variable. However, PowerShell returned no matching results.

This indicated that the variable was not found under that exact name in the file. Rather than assuming the application version was incorrect, I expanded my search to locate references to version `4.1.43` throughout the application's source code.

**PowerShell Command:**

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen\cps -Recurse -File | Select-String -Pattern "4\.1\.43" | Select-Object -First 10
```

**What this command does:**

The command recursively searches files within the application's `cps` directory for the version string `4.1.43` and displays the first 10 matching results.

**Expected Result:**

If the version string exists in the searched files, PowerShell will display the matching file paths, line numbers, and corresponding text.

**Why this matters:**

During vulnerability research, I need to verify information directly from the source rather than rely on assumptions about where configuration values are stored.

This search also demonstrates how I can investigate an unexpected result and adjust my approach without modifying the target application.

**Current Verification Status:**

| Baseline Component | Result | Status |
|---|---|---|
| Application | Calibre-Web NextGen | Confirmed |
| Git Branch | `main` | Verified |
| Commit Hash | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` | Verified |
| Working Tree | Clean | Verified |
| Application Version | `4.1.43` | Source reference search pending |

**Next Step:**

Review the PowerShell search results to determine where version `4.1.43` is referenced. A matching reference may provide supporting evidence, but I will verify that it represents the application's actual version before marking this check complete.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5d345134ab50ded4085c0ab361ac7eec81d9f393/Screenshot%202026-09-17%20093645.png)


### Step 6: Identify How the Application Is Launched

After searching the `cps` directory, I found no references to the literal version string `4.1.43`.

Since I had already verified the exact Git commit, active branch, and clean working tree, I decided to use the commit hash as my primary source-code baseline rather than spend additional time searching for a version string.

**Verified Source-Code Baseline:**

| Component | Result | Status |
|---|---|---|
| Application | Calibre-Web NextGen | Confirmed |
| Git Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` | Verified |
| Active Branch | `main` | Verified |
| Working Tree | Clean | Verified |
| Version String | Not found in searched `cps` files | Not independently verified |

### Inspect the Application's Root Directory

My next objective was to determine how the application is installed and launched so I could prepare a controlled dynamic testing environment.

I opened PowerShell and ran:

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen | Select-Object Name
```

**What this command does:**

This command lists the files and directories located at the root of the Calibre-Web NextGen source repository.

I used it to look for installation instructions, dependency files, Docker configuration, and application startup scripts.

**Why this matters:**

Before testing application security, I need to understand how the application runs and which services or dependencies are required.

Identifying the correct startup method helps me avoid unnecessary configuration changes and establish a repeatable testing environment.

**Current Result:**

The directory listing has not yet been reviewed. I will use the command output to identify the appropriate application startup method.

### Next Step

Review the root directory listing and determine whether the application provides a Docker-based deployment, a Python installation workflow, or another supported startup method.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/20b43b8dcd4051b7a584459a51609ba2d8a28745/Screenshot%202026-09-17%20093828.png)


### Step 7: Verify the Application Version Using the VERSION File

After examining the root directory of the Calibre-Web NextGen repository, I identified two important files:

- `cps.py` — a potential Python application entry point.
- `VERSION` — a file that may contain the application's version information.

My earlier searches within the `cps` directory returned no references to version `4.1.43`. Finding a dedicated `VERSION` file gave me a more direct way to verify the application's version.

### PowerShell Command

```powershell
Get-Content .\targets\Calibre-Web-NextGen\VERSION
```

**What this command does:**

The `Get-Content` command reads and displays the contents of the `VERSION` file without modifying it.

**Expected Result:**

```text
4.1.43
```

The actual output must be checked before confirming the version.

### Why This Matters

During vulnerability research, I need to establish a reliable baseline so my testing can be reproduced.

Verifying the application version alongside the Git commit, branch, and working tree helps document exactly which source code I am examining.

### Current Baseline Verification

| Component | Result | Status |
|---|---|---|
| Application | Calibre-Web NextGen | Confirmed |
| Git Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` | Verified |
| Branch | `main` | Verified |
| Working Tree | Clean | Verified |
| Application Version | `4.1.43` | Awaiting VERSION file output |

### Next Step: Prepare the Application for Dynamic Testing

Once I verify the contents of the `VERSION` file, my next objective is to identify the supported startup procedure and prepare the application for controlled dynamic security testing.

This will allow me to begin evaluating the potential vulnerabilities identified during Lab 4 against a running application.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/48627edfb05f26d35360a211477d4d0f86132331/Screenshot%202026-09-17%20094042.png)


### Step 8: Activate the Python Virtual Environment

After verifying the application version, I completed my Lab 5 source-code baseline verification.

### Verified Testing Baseline

| Component | Verified Result |
|---|---|
| Application | Calibre-Web NextGen |
| Version | `4.1.43` |
| Branch | `main` |
| Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` |
| Working Tree | Clean |

### Activate the Research Environment

Before attempting to launch the application, I needed to activate my existing Python virtual environment.

I opened PowerShell in my vulnerability research lab directory and ran:

```powershell
.\.venv\Scripts\Activate.ps1
```

**What this command does:**

This command activates the Python virtual environment named `.venv`.

The virtual environment helps isolate the Python interpreter and installed packages used for my research from other Python projects on my computer.

**Expected Result:**

After successful activation, PowerShell typically displays `(.venv)` at the beginning of the command prompt.

```text
(.venv) PS C:\Users\eelve\Vulnerability-Research-Lab>
```

**Why this matters:**

Using a dedicated virtual environment helps maintain a consistent research setup and reduces potential dependency conflicts between projects.

However, activating the environment does not automatically install the application's dependencies or start the application.

### Next Step

After confirming that my Python virtual environment is active, I will review the application's startup requirements and determine whether it can run directly through Python or requires a containerized deployment.

This will prepare me to move from source-code verification into hands-on dynamic security testing.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b917224140e49cc5985e38284c0e7efc2fa45c6f/Screenshot%202026-09-17%20094417.png)


### Step 9: Resolve the PowerShell Execution Policy Restriction

While attempting to activate my Python virtual environment, I encountered a PowerShell execution-policy restriction that prevented the activation script from running.

This did not necessarily indicate a problem with my virtual environment. Instead, PowerShell was restricting the execution of the `.ps1` activation script.

### Temporarily Allow Script Execution

To resolve the issue without permanently changing my system's execution policy, I ran:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

**What this command does:**

This command temporarily changes the execution policy for the current PowerShell session, allowing scripts to run without execution-policy restrictions.

The change applies only to the current PowerShell process and does not permanently modify the system-wide execution policy.

### Activate the Virtual Environment

After adjusting the execution policy, I ran:

```powershell
.\.venv\Scripts\Activate.ps1
```

**Expected Result:**

```text
(.venv) PS C:\Users\eelve\Vulnerability-Research-Lab>
```

The `(.venv)` prefix indicates that the Python virtual environment has been activated.

### Why This Matters

This troubleshooting step allowed me to address an environment configuration issue without modifying the Calibre-Web NextGen source code.

It also reinforced the importance of distinguishing between application failures and operating-system restrictions during security research.

### Security Considerations

Although the execution-policy change is temporary, `Bypass` disables PowerShell's execution-policy checks for that session.

I used this approach only for my trusted local activation script. I should close the PowerShell session when finished and avoid running untrusted scripts.

### Next Step

After confirming that the virtual environment is active, I will examine the application's dependencies and startup requirements before attempting to launch Calibre-Web NextGen for dynamic security testing.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/028f974aee69bccf74d43e0ffd2a2d0bed1e65b6/Screenshot%202026-09-17%20094721.png)


### Step 10: Examine the Application Startup Options

After resolving the PowerShell execution-policy restriction, I successfully activated my Python virtual environment.

**Confirmed Environment:**

```text
(.venv) PS C:\Users\eelve\Vulnerability-Research-Lab>
```

With my research environment active, my next objective was to identify the correct startup procedure for Calibre-Web NextGen before attempting to launch the application.

### Inspect the Application Entry Point

I opened PowerShell and ran:

```powershell
python .\targets\Calibre-Web-NextGen\cps.py --help
```

**What this command does:**

This command attempts to execute the application's `cps.py` entry point with the `--help` argument.

If the application supports this option and its required dependencies are available, it may display command-line usage information and supported startup parameters.

However, some Python applications initialize dependencies or services before processing command-line arguments, so this command is not guaranteed to display help without errors.

### Why This Matters

Before launching an application for dynamic security testing, I need to understand its startup requirements and dependencies.

This helps me:

- Identify the supported application startup method.
- Recognize missing dependencies or configuration issues.
- Avoid unnecessary changes to the source-code baseline.
- Prepare a consistent and reproducible testing environment.

### Current Status

| Component | Status |
|---|---|
| Source Code Baseline | Verified |
| Application Version | 4.1.43 |
| Python Virtual Environment | Active |
| PowerShell Execution Policy Restriction | Resolved for current session |
| Application Startup Options | Awaiting command output |
| Running Application | Not yet verified by this step |

### Next Step

I will examine the command output to determine whether the application supports direct Python execution or requires additional dependencies or a containerized environment.

Any errors will be documented as part of my environment preparation and troubleshooting process.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4fb21783450b1402ff962932d9327a1433a780ad/Screenshot%202026-09-17%20094933.png)


### Step 11: Investigate Missing Python Dependencies

After activating my Python virtual environment, I attempted to inspect the Calibre-Web NextGen application's startup options.

I ran:

```powershell
python .\targets\Calibre-Web-NextGen\cps.py --help
```

### Error Encountered

PowerShell returned the following error:

```text
ModuleNotFoundError: No module named 'flask'
```

**What this error means:**

The application reached its Python source code, but the active virtual environment could not locate the Flask package.

This indicated a missing dependency in my research environment rather than evidence of a security vulnerability.

### Investigate the Project's Dependency Configuration

Instead of installing Flask individually, I decided to examine the project's existing dependency configuration.

The repository contains a `pyproject.toml` file, which can define Python project metadata, dependencies, and build requirements.

I ran the following PowerShell command:

```powershell
Select-String -Path .\targets\Calibre-Web-NextGen\pyproject.toml -Pattern "dependencies|flask" -Context 2,5
```

**What this command does:**

The command searches `pyproject.toml` for references to `dependencies` or `flask`.

The `-Context 2,5` option displays two lines before and five lines after each match, allowing me to examine the surrounding configuration.

### Why This Matters

During vulnerability research, it is important to distinguish between application security weaknesses and problems with the local testing environment.

Installing individual packages without understanding the project's dependency requirements could introduce version conflicts or leave other required packages missing.

By inspecting the project's configuration first, I can identify its intended installation method and maintain a more consistent testing environment.

### Current Testing Status

| Component | Status |
|---|---|
| Calibre-Web NextGen Version | 4.1.43 — Verified |
| Git Commit | Verified |
| Git Branch | `main` — Verified |
| Working Tree | Clean |
| Python Virtual Environment | Active |
| Flask Dependency | Missing from active environment |
| Dependency Configuration | Under investigation |
| Dynamic Security Testing | Not yet started in this environment |

### Next Step

I will review the `pyproject.toml` search results to determine how the project declares its dependencies.

Once I understand the installation requirements, I can select the appropriate setup method before launching the application for controlled dynamic security testing.

**Finding:** The missing Flask package represents an environment setup issue, not a confirmed application vulnerability.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8873d733a065ab660fad60294b6bb545e6721da/Screenshot%202026-09-17%20095155.png)


### Step 12: Install Calibre-Web NextGen and Its Dependencies

After examining the `pyproject.toml` configuration, I confirmed that Calibre-Web NextGen declares its Python runtime dependencies, including Flask and authentication-related packages.

My previous attempt to inspect the application's startup options failed because Flask was missing from my active virtual environment.

Rather than installing individual dependencies manually, I decided to install the application using its existing project configuration.

### Install the Application in Editable Mode

With my Python virtual environment already activated, I ran the following command:

```powershell
python -m pip install -e .\targets\Calibre-Web-NextGen
```

**What this command does:**

- `python -m pip` runs the package installer using my active Python interpreter.
- `install` instructs pip to install the project and its declared dependencies.
- `-e` requests an editable installation, allowing Python to reference my local source checkout.
- `.\targets\Calibre-Web-NextGen` specifies the local application directory.

### Why I Chose an Editable Installation

Using an editable installation allows me to work directly with the authorized Calibre-Web NextGen source repository while installing its declared dependencies.

This approach is useful for vulnerability research because it allows me to examine source code and test application behavior against the same local checkout.

However, an editable installation does not guarantee that all dependencies will install successfully. Some packages may require additional system libraries or compilation tools.

### Security Research Significance

I wanted to maintain a reproducible environment and avoid introducing unnecessary changes to the application source code.

Installing dependencies through the project's configuration also reduces the risk of overlooking required packages.

### Current Status

| Component | Status |
|---|---|
| Target Application | Calibre-Web NextGen |
| Version | 4.1.43 — Verified |
| Git Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` |
| Branch | `main` |
| Working Tree | Clean before installation |
| Python Virtual Environment | Active |
| Dependency Installation | Pending verification |
| Application Startup | Not attempted after installation |

### Next Step

I will review the installation output to determine whether all required dependencies were installed successfully.

If the installation fails, I will document the error and investigate the dependency requirements before proceeding.

**At this stage, I have not confirmed that the application is ready to run or that any vulnerability is exploitable.**


![Inage alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/18665aca2ed3554c78e7b1ba219d8d4e0340f4c1/Screenshot%202026-09-17%20100759.png)


### Step 13: Diagnose the Python Dependency Installation Failure

During the editable installation of Calibre-Web NextGen, several Python packages built successfully. However, the overall installation failed because two dependencies could not be built:

- `python-ldap`
- `faust-cchardet`

Both packages may require native compiled components, so I needed to investigate the actual build errors before making changes to my research environment.

### Step 13A: Investigate the python-ldap Build Failure

My first objective was to isolate the `python-ldap` installation error and identify what prevented the package from building.

**PowerShell Command:**

```powershell
python -m pip install python-ldap -v
```

**What this command does:**

This command attempts to install `python-ldap` separately while enabling verbose output.

The `-v` option provides additional information about the installation process, including package preparation, compilation, and build failures.

### Error Messages to Investigate

I focused on identifying the first meaningful compiler or build error, particularly references to:

```text
error:
Microsoft Visual C++
cl.exe
lber.h
ldap.h
```

These messages could indicate missing compiler tools, LDAP development headers, or other native build requirements.

### Why This Matters

Rather than repeatedly attempting the full installation or installing unrelated packages, I wanted to identify the underlying dependency problem.

This troubleshooting approach helps me distinguish environment configuration failures from actual application security issues.

It also allows me to document the installation problem and determine whether a different supported deployment method is more appropriate.

### Current Status

| Component | Result |
|---|---|
| Target Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Virtual Environment | Active |
| Editable Installation | Failed |
| `python-ldap` | Build failure — cause under investigation |
| `faust-cchardet` | Build failure — cause not yet diagnosed |
| Dynamic Testing | Not started through this Python environment |

### Next Step

I will examine the verbose installation output and capture approximately 20–30 lines surrounding the first meaningful compiler error.

Once the cause is identified, I can determine the appropriate solution without unnecessarily modifying the application's source code.

**Finding:** The installation failure is a dependency build issue. It does not establish a security vulnerability in Calibre-Web NextGen.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d9c9cc19bf1268c7b2cddd4b7f8624dc17f4cb4d/Screenshot%202026-09-17%20101146.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4a822239a08e8f6256507fa1ae0970a78d57b739/Screenshot%202026-09-17%20101203.png)


### Step 14: Capture and Analyze the python-ldap Build Error

After attempting to install `python-ldap` with verbose logging, I confirmed that the package failed to build. However, the PowerShell output only showed the final installation failure summary.

To determine the actual cause, I needed to capture the complete installation log and search for compiler-related errors.

### Step 14A: Save the Full Installation Output

I ran the following command in PowerShell:

```powershell
python -m pip install python-ldap -v *> .\python-ldap-build.txt
```

**What this command does:**

This command attempts to install `python-ldap` with verbose logging and redirects the PowerShell output streams into a text file named `python-ldap-build.txt`.

Saving the output allows me to review the installation failure without losing important messages when the terminal scrolls.

### Step 14B: Search for Compiler Errors

After capturing the installation log, I used PowerShell to search for specific error messages:

```powershell
Select-String -Path .\python-ldap-build.txt -Pattern "fatal error|error C|ldap.h|lber.h|cl.exe|Microsoft Visual C" -Context 3,3
```

**What this command does:**

The command searches the saved installation log for messages commonly associated with native Python package build failures.

The `-Context 3,3` parameter displays three lines before and three lines after each match, helping me understand the surrounding error information.

### Errors Under Investigation

| Error Indicator | Possible Explanation |
|---|---|
| `Microsoft Visual C++` | Required compiler or build tools may be missing |
| `cl.exe` | Microsoft C/C++ compiler may be unavailable or encountering an error |
| `ldap.h` | LDAP development header may be missing |
| `lber.h` | LDAP library development header may be missing |
| `fatal error` | Compilation stopped because of an error |
| `error C` | Microsoft compiler reported a compilation error |

These are possible causes, not confirmed findings.

### Why This Matters

During vulnerability research, I need to troubleshoot environmental problems methodically instead of making unnecessary configuration changes.

Capturing the full error log allows me to identify the underlying dependency failure before deciding whether to install additional build tools, investigate Python compatibility, or use the application's supported containerized deployment.

### Current Findings

The editable installation of Calibre-Web NextGen was unsuccessful because `python-ldap` and `faust-cchardet` failed to build.

The exact compiler error has not yet been established from the available output.

### Next Step

I will review the extracted compiler messages and identify the first meaningful error.

I will not install additional dependencies, change Python versions, or modify the application's source code until I understand the cause.

**Security Research Note:** This remains an environment dependency issue, not a confirmed vulnerability in Calibre-Web NextGen.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a1cef7e4bb4eb2c1ac5babd273801aafe89b70a4/Screenshot%202026-09-17%20101407.png)


### Step 15: Identify an Alternative Application Startup Method

After reviewing the `python-ldap` installation log, I identified the exact cause of the build failure.

**Confirmed Error:**

```text
error: Microsoft Visual C++ 14.0 or greater is required.
```

This error indicated that my Windows Python environment did not have the compiler tools required to build `python-ldap`.

### Investigate an Alternative Installation Method

Because `python-ldap` supports LDAP authentication functionality, I wanted to determine whether Calibre-Web NextGen could be launched without compiling that dependency locally.

LDAP authentication was also one of my Lab 4 security research candidates, so I planned to evaluate its requirements separately if necessary.

Rather than immediately installing a large compiler toolchain, I decided to inspect the repository for its documented installation and startup options.

**PowerShell Command:**

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen -Name | Select-String "README|requirements|setup|docker"
```

**What this command does:**

This command lists files and directories in the root of the Calibre-Web NextGen repository and filters their names for references to:

- `README` — project documentation and installation instructions.
- `requirements` — Python dependency configuration.
- `setup` — installation or configuration files.
- `docker` — possible container deployment configuration.

### Why This Matters

My goal was to find a supported installation method that would allow me to continue dynamic security testing without unnecessarily modifying my Windows environment.

A containerized deployment could provide an alternative to compiling native Python dependencies directly on Windows, but I needed to confirm that the repository supported this approach.

### Troubleshooting Summary

| Component | Finding |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Source-Code Baseline | Verified |
| Python Virtual Environment | Active |
| Editable Installation | Failed |
| `python-ldap` | Microsoft Visual C++ 14.0+ required |
| `faust-cchardet` | Build failure also observed |
| Alternative Startup Method | Under investigation |

### Next Step

I will review the matching repository files and identify the installation procedure recommended by the project maintainers.

If the application supports Docker, I will evaluate that option for my controlled testing environment.

**Outcome:** I successfully isolated the `python-ldap` compiler requirement. Rather than treating the installation error as an application vulnerability, I continued investigating a supported deployment method that would allow me to proceed with Lab 5.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/8a445e77ebe82afdaa1d93b9711ce053f4f12922/Screenshot%202026-09-17%20103709.png)


### Step 16: Review the Application's Installation Instructions

After identifying the `python-ldap` build failure, I confirmed that the Calibre-Web NextGen repository included documentation for both standard installation and Docker deployment.

Because my computer had limited available disk space, I wanted to avoid installing unnecessary development tools or additional software before reviewing the project's recommended setup procedures.

My goal was to find a supported installation method that would allow me to continue testing the security candidates identified during Lab 4.

### Examine the README Installation Instructions

I used PowerShell to search the project's README for installation-related information.

**PowerShell Command:**

```powershell
Select-String -Path .\targets\Calibre-Web-NextGen\README.md -Pattern "install|Windows|pip|Docker" -Context 3,6
```

**What this command does:**

This command searches the project's `README.md` file for references to installation procedures, Windows compatibility, Python package installation, and Docker deployment.

The `-Context 3,6` parameter displays three lines before and six lines after each match, allowing me to review the surrounding instructions.

### Why This Matters

Before selecting a deployment method, I needed to understand the application's documented requirements.

My earlier attempt to install the application directly through Python encountered a native dependency compilation error. Rather than making additional changes without understanding the requirements, I continued investigating the installation options provided by the project.

This approach helps me maintain a reproducible testing environment while minimizing unnecessary software installations.

### Dynamic Testing Objectives

Once the application is running, I plan to investigate the following Lab 4 candidates:

| Candidate | Security Area | Testing Objective |
|---|---|---|
| #1 | Read-Status Authorization | Determine whether a user can modify another restricted book's read status |
| #2 | Archive-Status Authorization | Evaluate whether archive-status changes enforce object-level access controls |
| #5 | Shelf Redirect Handling | Examine whether redirect behavior improperly trusts the HTTP Referer header |
| #6 | Administrative SQL Handling | Evaluate whether user-controlled input can affect SQL expression construction |

The LDAP authentication candidate will be evaluated separately if additional LDAP-specific dependencies or configuration are required.

**Important:** These are research hypotheses identified during static analysis. None of the listed behaviors should be considered confirmed vulnerabilities without successful reproduction and supporting evidence.

### Current Environment Assessment

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Branch | `main` |
| Working Tree | Clean at baseline verification |
| Python Virtual Environment | Active |
| Editable Python Installation | Failed |
| `python-ldap` Build Error | Microsoft Visual C++ 14.0+ required |
| Docker Documentation | Identified |
| Standard Installation Documentation | Identified |
| Installation Method | Awaiting README review |

### Next Step

I will examine the README search results and compare the documented installation requirements.

My objective is to select a practical, supported deployment method that allows me to test the application locally without unnecessary changes to my system.

**Outcome:** I identified the dependency limitation and continued researching the project's supported startup options before proceeding with dynamic security testing.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a6cfe80198240bcb84a42a583f1eda6200b33700/Screenshot%202026-09-17%20103937.png)


### Step 17: Assess Available Disk Space Before Deployment

After reviewing the Calibre-Web NextGen installation documentation, I confirmed that the project's Quick Start procedure uses Docker and Docker Compose.

The documented deployment command was:

```powershell
docker compose up -d
```

The documentation indicated that the application would be accessible at `http://localhost:8083` using the default configuration.

### Why I Checked Storage First

My earlier attempt to install Calibre-Web NextGen directly through Python failed because the `python-ldap` dependency required Microsoft Visual C++ 14.0 or greater.

Since my computer had limited storage, I wanted to avoid installing additional development tools or starting a Docker deployment without first checking the available disk space.

### Check Available Storage

I opened PowerShell and ran:

```powershell
Get-PSDrive C | Select-Object Used,Free
```

**What this command does:**

This command retrieves storage information for the `C:` drive and displays two values:

- `Used` — the amount of storage currently occupied.
- `Free` — the amount of storage still available.

PowerShell typically reports these values in bytes.

### Why This Matters for Security Testing

Dynamic application security testing requires a stable environment. Insufficient disk space could interfere with dependency installation, container image downloads, application startup, database operations, and evidence collection.

Checking storage before deployment helps me select a practical installation method without unnecessarily affecting my existing research environment.

### Current Environment Status

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Branch | `main` |
| Source Working Tree | Clean at baseline verification |
| Python Virtual Environment | Active |
| Direct Python Installation | Blocked by dependency build errors |
| Docker Quick Start | Confirmed in project documentation |
| Available Disk Space | Awaiting PowerShell output |
| Deployment Method | Not yet selected at this stage |

### Next Step

I will review the available storage reported by PowerShell before deciding how to deploy the application.

My objective is to establish a reliable local testing environment while minimizing additional storage requirements.

**Outcome:** I confirmed the project's documented Docker startup procedure and identified disk capacity as a deployment consideration that needed to be evaluated before continuing.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/51a05c4c82922567a1d9482f59c31343800ccda4/Screenshot%202026-09-17%20104140.png)


### Step 18: Check for Existing Container Tools

After checking the available storage on my computer, I confirmed that my `C:` drive had sufficient free space to support additional software installations.

### Disk Space Verification

| Component | Result |
|---|---|
| Drive | `C:` |
| Available Space | 50,713,722,888 bytes |
| Available Space (GiB) | Approximately 47.2 GiB |
| Storage Status | Sufficient for further evaluation |

Although my computer had enough available storage, I wanted to avoid installing a large Visual Studio development workload solely to resolve the `python-ldap` compilation error.

Since Calibre-Web NextGen supports container-based deployment, I decided to check whether Docker or Podman was already installed on my system.

### Check Docker Installation

I opened PowerShell and ran:

```powershell
docker --version
```

**What this command does:**

This command checks whether the Docker command-line interface is available and displays its version.

A successful response confirms that the Docker CLI is installed, but does not necessarily mean the Docker engine is running.

### Check Podman Installation

Next, I ran:

```powershell
podman --version
```

**What this command does:**

This command checks whether the Podman command-line interface is available and displays its version.

Podman is another container management tool that may provide an alternative deployment option, depending on the application's configuration.

### Why This Matters

Before installing additional software, I wanted to identify tools already available in my research environment.

Reusing an existing container runtime could help me avoid unnecessary installations while following the application's documented deployment approach.

### Current Assessment

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 verified |
| Git Commit | Verified |
| Working Tree | Clean at baseline verification |
| Python Virtual Environment | Active |
| Direct Python Installation | Blocked by dependency build errors |
| Available C: Storage | Approximately 47.2 GiB |
| Docker Installation | Awaiting command output |
| Podman Installation | Awaiting command output |
| Deployment Method | Pending tool verification |

### Next Step

I will review the Docker and Podman version results to determine whether an existing container tool can be used to launch Calibre-Web NextGen.

If neither tool is available, I will evaluate the installation requirements before making changes to my system.

**Outcome:** I verified that disk capacity was not the immediate limitation and continued evaluating a supported, practical deployment method for my dynamic security testing lab.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c214a14336f548eb66b5a96deb415e0edb9d742/Screenshot%202026-09-17%20104558.png)


### Step 19: Investigate Optional LDAP Dependencies

After checking my Windows environment, I determined that neither Docker nor Podman was installed at this stage of the lab.

Rather than immediately installing a container runtime or additional development tools, I decided to investigate whether Calibre-Web NextGen could run without its LDAP authentication dependencies.

My objective was to determine whether I could continue testing the other security candidates using my existing Python virtual environment.

### Inspect LDAP Imports in the Source Code

I opened PowerShell and ran:

```powershell
Select-String -Path .\targets\Calibre-Web-NextGen\cps\*.py -Pattern "import ldap|from ldap|flask_simpleldap|SimpleLDAP" -Context 2,3
```

**What this command does:**

This command searches Python files directly inside the application's `cps` directory for LDAP-related imports and references.

The `-Context 2,3` option displays two lines before and three lines after each match, helping me determine how LDAP functionality is integrated into the application.

### What I Was Investigating

I wanted to determine whether LDAP dependencies were loaded automatically when the application started or imported conditionally when LDAP authentication was enabled.

| Possible Finding | Security Research Significance |
|---|---|
| Conditional LDAP import | May allow startup without LDAP support |
| Unconditional LDAP import | May require LDAP packages even when LDAP authentication is disabled |
| LDAP feature configuration | Could identify a supported way to disable optional authentication functionality |
| No matching imports | Would require searching additional files and subdirectories |

### Why This Matters

My earlier editable installation failed because `python-ldap` required Microsoft Visual C++ 14.0 or greater.

If LDAP functionality is optional and the application supports running without it, I may be able to continue investigating the other Lab 4 security candidates without installing additional development tools.

However, conditional imports alone would not guarantee a successful installation because `pip` may still require the dependency declared in `pyproject.toml`.

I also needed to account for the separate `faust-cchardet` build failure.

### Current Findings

- The Calibre-Web NextGen 4.1.43 source-code baseline was verified.
- The Python virtual environment was successfully activated.
- The editable installation failed because of native dependency build errors.
- Docker and Podman were not installed at this stage.
- LDAP import behavior had not yet been verified.

### Next Step

I will examine the source-code search results to determine whether LDAP authentication is optional at runtime.

If the application supports disabling LDAP cleanly, I will evaluate that approach without modifying the target source code.

**Outcome:** This step investigates dependency loading and startup requirements. It does not establish a security vulnerability.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/90f13224abf8ee5546af2f89d11791704dced89d/Screenshot%202026-09-17%20104752.png)


### Step 20: Search Recursively for LDAP Dependencies

During my initial LDAP investigation, I searched Python files directly inside the `cps` directory.

The search identified an LDAP-related reference in `web.py`:

```python
ldap_user_details = services.ldap.get_object_details(username)
```

However, this was a call to the application's LDAP service, not an actual Python import statement.

Because my original search did not include subdirectories, I needed to expand the investigation to determine where LDAP dependencies were imported.

### Step 20A: Search All Python Files Recursively

I used PowerShell to search the entire `cps` directory, including its subdirectories.

**PowerShell Command:**

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen\cps -Recurse -Filter *.py | Select-String -Pattern "import ldap|from ldap|flask_simpleldap|SimpleLDAP" -Context 2,3
```

**What this command does:**

This command recursively identifies Python files within the application's `cps` directory and searches them for LDAP-related import statements and class references.

The `-Context 2,3` parameter displays two lines before and three lines after each match, helping me understand how the dependency is loaded.

### What I Wanted to Determine

My objective was to identify whether the application loads LDAP dependencies unconditionally during startup or only when LDAP authentication is enabled.

| Possible Result | Interpretation |
|---|---|
| LDAP imported conditionally | The application may support running without LDAP functionality |
| LDAP imported unconditionally | LDAP dependencies may be required during application startup |
| LDAP imports found in service modules | Additional investigation of service initialization may be necessary |
| No matching imports | Further investigation of dependency loading may be required |

### Why This Matters

My previous installation attempt failed because the `python-ldap` package required Microsoft Visual C++ 14.0 or greater.

Before installing additional development tools, I wanted to determine whether LDAP authentication could be excluded through an existing, supported application configuration.

If LDAP support is optional, I may be able to proceed with dynamic testing of other candidates while postponing Candidate #4, which focuses on LDAP authentication behavior.

However, locating conditional imports would not automatically resolve the project's package installation requirements or the separate `faust-cchardet` build failure.

### Current Investigation Status

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Python Virtual Environment | Active |
| Editable Installation | Failed |
| `python-ldap` Compiler Requirement | Confirmed |
| Initial LDAP Search | Service reference identified |
| Recursive LDAP Import Search | Awaiting results |
| LDAP-Free Startup | Not yet verified |

### Next Step

I will examine the recursive search results to identify the actual LDAP import locations and determine whether the application provides a supported mechanism for disabling LDAP functionality.

**Outcome:** This step expands my source-code investigation to understand dependency loading before selecting the next deployment approach.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8dc5a01f3a5ac979fdcacdb80a305d67e7da484/Screenshot%202026-09-17%20104932.png)


### Step 21: Confirm Optional LDAP Support and Investigate faust-cchardet

After recursively searching the Calibre-Web NextGen source code, I identified how the application handles LDAP authentication dependencies.

In `cps/services/__init__.py`, the application uses a `try/except ImportError` block:

```python
try:
    from . import simpleldap as ldap
    from .simpleldap import ldapVersion
except ImportError as err:
    log.debug("Cannot import simpleldap, logging in with ldap will not work: %s", err)
    ldap = None
    ldapVersion = None
```

### What I Discovered

This code demonstrates that the application is designed to handle a missing LDAP module without necessarily stopping service initialization.

If the import fails, the application sets `ldap` and `ldapVersion` to `None` and records a debug message.

**My conclusion:** LDAP authentication is optional at runtime for this import path. This means I can postpone Lab 4 Candidate #4, which focuses on LDAP authentication behavior, while investigating whether the remaining application functionality can run without `python-ldap`.

However, this does not automatically mean the package installer will exclude LDAP dependencies or that the entire application can start successfully.

### Step 21A: Diagnose the faust-cchardet Installation Failure

My original editable installation failed because two packages could not be built:

- `python-ldap` — confirmed Microsoft Visual C++ 14.0+ requirement.
- `faust-cchardet` — exact failure cause not yet established.

Since I had identified the application's optional LDAP import handling, my next objective was to isolate the remaining `faust-cchardet` installation error.

**PowerShell Command — Capture the Full Build Log:**

```powershell
python -m pip install faust-cchardet -v *> .\faust-cchardet-build.txt
```

**What this command does:**

This command attempts to install `faust-cchardet` with verbose output and saves the installation messages to `faust-cchardet-build.txt`.

This gives me a complete record of the build process, including errors that might otherwise disappear as the terminal scrolls.

### Step 21B: Extract the Relevant Build Errors

After saving the installation log, I ran:

```powershell
Select-String -Path .\faust-cchardet-build.txt -Pattern "fatal error|error C|Microsoft Visual C|Failed|ERROR" -Context 3,3
```

**What this command does:**

The command searches the build log for compiler errors and installation failure messages.

The `-Context 3,3` parameter includes three lines before and after each match, helping me identify the cause of the failure.

### What I Am Investigating

| Possible Error | Potential Cause |
|---|---|
| Microsoft Visual C++ required | Missing native compiler toolchain |
| Compiler error | C extension compilation failure |
| Missing header file | Required development headers unavailable |
| Python compatibility error | Package may not support my Python version |
| Build dependency failure | A required build package could not be installed |

These are possible explanations until the installation log confirms the actual error.

### Why This Matters

Rather than installing a large compiler toolchain immediately, I wanted to understand whether the remaining dependency failure could be resolved through a supported installation method.

My approach was to isolate each issue, preserve the verified application source code, and avoid unnecessary changes to my research environment.

### Current Findings

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 verified |
| Git Commit | Verified |
| Working Tree | Clean at baseline verification |
| Python Virtual Environment | Active |
| `python-ldap` | Missing Microsoft Visual C++ compiler |
| LDAP Import Handling | Optional import behavior confirmed |
| LDAP Security Candidate #4 | Postponed |
| `faust-cchardet` | Build error under investigation |
| Dynamic Testing Environment | Not yet running through Python |

### Next Step

I will review the `faust-cchardet` build log to identify its exact installation failure.

If the error is related to native compilation, I will investigate whether the project supports an alternative dependency or installation method before changing the environment.

**Outcome:** I confirmed that the application handles missing LDAP imports and isolated `faust-cchardet` as the next dependency requiring investigation. No application vulnerability has been confirmed by these installation errors.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ae7ec2996c3933caf7dffb5378e142373611d76a/Screenshot%202026-09-17%20105315.png)


### Step 22: Review the faust-cchardet Installation Log

After capturing the verbose installation output for `faust-cchardet`, I searched the log for common compiler errors and installation failure messages.

The search returned no matching results.

This meant the log did not contain any of the specific error patterns I searched for. However, it did not confirm whether the package had installed successfully.

### Examine the Final 40 Lines

To investigate further, I used PowerShell to display the end of the installation log.

**PowerShell Command:**

```powershell
Get-Content .\faust-cchardet-build.txt -Tail 40
```

**What this command does:**

The `Get-Content` command reads the saved installation log, while `-Tail 40` displays its final 40 lines.

This allows me to inspect the installation outcome without reviewing the entire log.

### What I Am Looking For

I want to determine whether:

- The package installed successfully.
- The installation failed because of a dependency or compatibility issue.
- The build stopped for a reason not captured by my earlier search patterns.

### Why This Matters

During vulnerability research, I need to confirm the cause of environment issues before applying fixes.

Reviewing the installation log helps me avoid unnecessary software installations and keeps my troubleshooting process evidence-based.

### Current Status

| Component | Finding |
|---|---|
| `python-ldap` | Microsoft Visual C++ 14.0+ requirement confirmed |
| LDAP Runtime Support | Missing module handled by optional import logic |
| `faust-cchardet` | Final installation result not yet confirmed |
| Dynamic Testing Environment | Python startup not yet established |

### Next Step

I will review the final 40 lines of `faust-cchardet-build.txt` and document the actual installation result before deciding whether additional troubleshooting is necessary.

**Outcome:** The initial error search returned no matches. Further log inspection is required before concluding whether `faust-cchardet` remains a blocker.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/34ed439b886db0c4d363171567d6f1c064537472/Screenshot%202026-09-17%20110110.png)


### Step 23: Verify the Core Python Environment

After reviewing the `faust-cchardet` installation log, I confirmed that the package installed successfully.

**Confirmed Installation Result:**

```text
Successfully installed faust-cchardet-3.2.0
```

This resolved one of the two dependency build failures encountered during my original Calibre-Web NextGen installation.

The remaining known issue was `python-ldap`, which required Microsoft Visual C++ 14.0 or greater to compile.

My earlier source-code investigation also confirmed that Calibre-Web NextGen handles missing LDAP imports through an `ImportError` exception handler. This allowed me to investigate running the application without LDAP authentication support.

### Verify Flask and SQLAlchemy

Before attempting another application startup, I needed to confirm that the core Python packages were available in my active virtual environment.

**PowerShell Command:**

```powershell
python -c "import flask, sqlalchemy; print('Flask:', flask.__version__); print('SQLAlchemy:', sqlalchemy.__version__)"
```

**What this command does:**

This command attempts to import two important Python libraries:

- **Flask:** The web framework used by the application.
- **SQLAlchemy:** A database toolkit used for database interactions.

If both imports succeed, the command prints their installed version numbers.

### Expected Result

If both packages are installed correctly, PowerShell should display output similar to:

```text
Flask: <installed version>
SQLAlchemy: <installed version>
```

The actual version numbers must be confirmed from the command output.

If either package is missing, Python will return an import error.

### Why This Matters

Before launching an application for dynamic security testing, I need to confirm that its core dependencies are available.

Verifying Flask and SQLAlchemy helps me determine whether the Python environment is ready for further startup testing.

However, successful imports alone do not guarantee that every required application dependency is installed or that Calibre-Web NextGen will start successfully.

### Current Environment Status

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Python Virtual Environment | Active |
| `faust-cchardet` | Successfully installed — 3.2.0 |
| `python-ldap` | Installation blocked by C++ compiler requirement |
| LDAP Import Handling | Optional import behavior confirmed |
| Flask | Awaiting import verification |
| SQLAlchemy | Awaiting import verification |
| Application Startup | Not yet verified |

### Next Step

I will review the Flask and SQLAlchemy import results before proceeding with application startup troubleshooting.

My objective remains to establish a working local dynamic testing environment without unnecessarily installing the Visual Studio C++ development workload.

**Outcome:** I successfully resolved the `faust-cchardet` installation issue and moved on to validating the core Python environment.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ff5fe89255c860e0b2e16236c3a2825ebf69ce46/Screenshot%202026-09-17%20110322.png)


### Step 24: Inspect Installed Python Packages

After attempting to verify Flask and SQLAlchemy, I discovered that Flask was still missing from my active Python virtual environment.

This indicated that my earlier editable installation of Calibre-Web NextGen had not successfully installed all the application's required dependencies.

Although I had successfully installed `faust-cchardet` version `3.2.0`, the overall Python environment was not yet ready to run the application.

### Check Installed Packages

Before attempting another installation, I decided to review the packages already available in my virtual environment.

**PowerShell Command:**

```powershell
python -m pip list
```

**What this command does:**

This command displays the Python packages currently installed in my active virtual environment, along with their version numbers.

I used this information to identify which dependencies were already present and which packages might still be missing.

### Why This Matters

Calibre-Web NextGen requires multiple Python libraries to operate correctly.

Installing Flask individually would not necessarily resolve the other missing dependencies. I wanted to avoid creating an inconsistent environment by installing packages without first understanding the project's complete requirements.

Although `pip` supports editable installation without dependency resolution through `--no-deps`, that option does not install missing dependencies. I would need to verify the required packages separately before considering that approach.

### Current Environment Status

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Python Virtual Environment | Active |
| `faust-cchardet` | Installed — 3.2.0 |
| `python-ldap` | Installation blocked by C++ compiler requirement |
| Flask | Missing |
| SQLAlchemy | Not yet confirmed |
| Installed Package Inventory | Awaiting review |
| Application Startup | Not yet successful |

### Next Step

I will examine the output of `python -m pip list` and compare the installed packages against the dependencies declared in the application's `pyproject.toml`.

This will help me determine the next installation step without unnecessarily modifying the target source code or installing the Visual Studio C++ development workload.

**Outcome:** I identified that the Python environment was still missing required application dependencies and moved to inventory the installed packages before attempting another installation.


![iamge alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/adbbc4b561287613263653157a10c216d694d2e8/Screenshot%202026-09-17%20110510.png)


### Step 25: Inspect the Complete Runtime Dependency Configuration

After reviewing the installed Python packages, I confirmed that my research environment contained tools such as `faust-cchardet`, but was still missing essential Calibre-Web NextGen runtime dependencies, including Flask.

Rather than installing each missing package individually, I decided to examine the complete dependency configuration in `pyproject.toml`.

My objective was to determine whether the application provides a supported installation option that excludes LDAP authentication dependencies.

### Inspect the Runtime Dependency Definitions

I opened PowerShell in my vulnerability research lab directory and ran:

```powershell
Get-Content .\targets\Calibre-Web-NextGen\pyproject.toml | Select-Object -Skip 30 -First 90
```

**What this command does:**

This command reads the project's `pyproject.toml` file, skips the first 30 lines, and displays the following 90 lines.

I used it to inspect the runtime dependency declarations and identify any optional dependency groups or installation configurations.

### What I Am Investigating

I want to determine whether the project separates its core application requirements from optional authentication features.

| Dependency Area | Investigation Objective |
|---|---|
| Core web framework | Identify Flask and related dependencies |
| Database functionality | Identify SQLAlchemy and supporting packages |
| Authentication | Determine how LDAP dependencies are declared |
| Optional dependency groups | Check for supported non-LDAP installation options |
| Package version requirements | Identify version constraints needed for a consistent installation |

### Why This Matters

My original editable installation failed because `python-ldap` required Microsoft Visual C++ 14.0 or greater.

A source-code review subsequently confirmed that the application handles missing LDAP imports through an `ImportError` exception handler.

However, optional runtime behavior does not necessarily mean a dependency is optional during package installation.

I therefore needed to inspect the project's declared requirements before attempting another installation.

### Current Environment Status

| Component | Status |
|---|---|
| Application | Calibre-Web NextGen 4.1.43 |
| Git Commit | Verified |
| Python Virtual Environment | Active |
| `faust-cchardet` | Successfully installed — 3.2.0 |
| `python-ldap` | Native compilation blocked |
| Flask | Missing |
| SQLAlchemy | Not confirmed installed |
| Non-LDAP Dependency Group | Under investigation |
| Application Startup | Not yet successful |

### Next Step

I will review the dependency definitions to determine whether the project provides a supported installation method that excludes LDAP functionality.

If no such option exists, I will evaluate alternative installation approaches without changing the verified application source code.

**Outcome:** I moved from troubleshooting individual package failures to examining the application's complete dependency configuration so I could select a consistent and reproducible installation method.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/251855a2701af756853d92da4518c65c1327b673/Screenshot%202026-09-17%20110708.png)


### Step 26: Review the Official Calibre-Web NextGen Setup Guide

During my dependency investigation, I confirmed that Calibre-Web NextGen did not provide a separate non-LDAP dependency group in the configuration I reviewed.

I also identified a version mismatch involving `faust-cchardet`.

The project declares:

```text
faust-cchardet>=2.1.18,<2.1.20
```

However, I had manually installed:

```text
faust-cchardet==3.2.0
```

Although version `3.2.0` installed successfully, it does not satisfy the application's declared version constraint.

### Why This Matters

For accurate vulnerability research, I need to preserve the target application's intended dependency configuration.

Forcing incompatible dependency versions or modifying `pyproject.toml` could introduce behavior that differs from the officially supported application environment.

Instead, I decided to investigate the repository's documented setup procedures.

### Step 26A: Inspect AI_README.md

The main project README references `AI_README.md` as a setup guide intended for an AI assistant to follow.

I used PowerShell to inspect the beginning of that file.

**PowerShell Command:**

```powershell
Get-Content .\targets\Calibre-Web-NextGen\AI_README.md -First 120
```

**What this command does:**

- Reads the project's `AI_README.md` file.
- Displays its first 120 lines.
- Allows me to review the documented setup instructions without modifying the repository.

### What I Am Investigating

| Area | Research Objective |
|---|---|
| Windows installation | Determine whether native Windows setup is supported |
| Linux installation | Identify documented bare-metal installation requirements |
| Docker deployment | Determine whether containers are required or recommended |
| Dependency management | Identify the project's intended installation process |
| Storage requirements | Avoid unnecessary large downloads |
| WSL2 compatibility | Determine whether an existing Linux environment could support the application |

### Current Findings

**Confirmed:**

- The target version is Calibre-Web NextGen `4.1.43`.
- The original editable installation did not complete successfully.
- `python-ldap` failed because a required native compiler was unavailable.
- The application contains optional LDAP import handling.
- `faust-cchardet` version `3.2.0` installed successfully but does not satisfy the project's declared version constraint.
- No separate non-LDAP dependency group was identified in the inspected configuration.

**Not Yet Confirmed:**

- Whether `AI_README.md` documents a supported Windows installation.
- Whether WSL2 is already installed and configured.
- Whether a lower-storage deployment option is available.
- Whether the application can start successfully in the current environment.

### Next Step

I will review the first 120 lines of `AI_README.md` before selecting another installation method.

If the documentation recommends Linux or container deployment, I will first investigate existing WSL2 availability and storage requirements rather than immediately downloading Docker Desktop.

**Outcome:** I identified a dependency version mismatch and prioritized the application's official setup documentation to preserve the validity and reproducibility of my dynamic vulnerability research.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1965acfd6e3ad7ebb00e93446b759faa9b340745/Screenshot%202026-09-17%20110926.png)


### Step 27: Check WSL2 Availability Before Installing Docker

After reviewing the Calibre-Web NextGen setup documentation, I confirmed that the project's recommended Windows environment uses Docker Desktop with WSL2.

This changed my installation approach. Rather than continuing to troubleshoot native Python dependencies, I decided to follow the project's documented deployment method.

### Why I Changed My Approach

My earlier Python installation attempts encountered several dependency issues:

- `python-ldap` required Microsoft Visual C++ 14.0 or greater.
- The manually installed `faust-cchardet` version did not satisfy the project's declared version constraints.
- Flask and other core runtime dependencies were still missing.

Using the documented deployment environment would help me avoid unnecessary modifications to the application's dependency configuration.

### Step 27A: Check Windows Subsystem for Linux

Before downloading Docker Desktop, I needed to determine whether WSL was already configured on my Windows 11 computer.

**PowerShell Command:**

```powershell
wsl --status
```

**What this command does:**

The command checks the current Windows Subsystem for Linux configuration.

Depending on the system, it may display information such as the default WSL version, default Linux distribution, or whether WSL requires installation or configuration.

### What I Am Looking For

| Possible Result | Meaning |
|---|---|
| Default Version: 2 | WSL2 is configured as the default for new distributions |
| Default Distribution listed | A Linux distribution is configured |
| WSL not installed | WSL installation may be necessary |
| WSL configuration error | Additional investigation is required |

The output of `wsl --status` alone does not confirm that Docker Desktop is installed or that a Linux distribution is running.

### Storage Considerations

My earlier storage check showed approximately **47.2 GiB of free space** on the C: drive.

Before installing additional virtualization or container software, I wanted to verify what was already available and avoid unnecessary downloads.

### Current Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| Recommended Windows Deployment | Docker Desktop + WSL2 |
| Available C: Storage | Approximately 47.2 GiB at last check |
| WSL2 | Awaiting verification |
| Docker Desktop | Not installed at earlier environment check |
| Local Application Startup | Not yet verified |

### Next Step

I will examine the output of `wsl --status` to determine whether WSL2 is already configured.

Based on those results, I will identify the minimum additional setup required before deploying Calibre-Web NextGen in its documented environment.

**Outcome:** I selected the project's recommended Windows deployment approach and began checking existing system capabilities before installing additional software.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d74e1eae4bf30e8f19eaab56ecc8d157aaf587fa/Screenshot%202026-09-17%20111128.png)


### Step 28: Verify Installed WSL Linux Distributions

After running `wsl --status`, I confirmed that Windows Subsystem for Linux was configured with **Default Version: 2**.

This was an important finding because Calibre-Web NextGen's documented Windows deployment uses Docker Desktop with WSL2.

Before installing additional software, I needed to determine whether a Linux distribution was already available.

### Step 28A: List Installed Linux Distributions

**PowerShell Command:**

```powershell
wsl --list --verbose
```

**What this command does:**

This command displays the Linux distributions registered with Windows Subsystem for Linux.

The output includes:

- **NAME:** The installed Linux distribution.
- **STATE:** Whether the distribution is running or stopped.
- **VERSION:** Whether the distribution uses WSL1 or WSL2.

### Expected Output

If Ubuntu is installed with WSL2, the output may resemble:

```text
  NAME       STATE      VERSION
* Ubuntu     Stopped    2
```

This is an example, not a verified result from my environment.

### Why This Matters

A Linux distribution running under WSL2 could reduce the amount of additional setup required for my local testing environment.

However, having WSL2 configured does not automatically mean Ubuntu or another Linux distribution is installed.

I also wanted to avoid downloading Docker Desktop, updating the WSL kernel, or making unnecessary system changes before confirming my existing configuration.

### Current Environment Status

| Component | Status |
|---|---|
| Target Application | Calibre-Web NextGen 4.1.43 |
| WSL | Available |
| Default WSL Version | 2 — Confirmed |
| Linux Distribution | Awaiting verification |
| Docker Desktop | Installation deferred |
| WSL Kernel Update | Not performed |
| Local Application Startup | Not yet verified |

### Next Step

I will review the output of `wsl --list --verbose`.

If an existing Linux distribution uses WSL2, I can account for that in the deployment plan before determining whether any additional software is required.

**Outcome:** I confirmed that WSL2 was configured and proceeded to inventory installed Linux distributions without modifying the system.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0aeaa48334f76f493a7167d4ff7f76613086671d/Screenshot%202026-09-17%20111432.png)


### Step 29: Identify Available Ubuntu Distributions for WSL2

After checking my Windows Subsystem for Linux configuration, I confirmed the following:

- **WSL2:** Installed and configured.
- **Default WSL version:** 2.
- **Linux distribution:** Not installed.
- **Docker:** Not installed at this stage.

Because Calibre-Web NextGen's Windows setup guide recommends Docker Desktop with WSL2, I decided to investigate the available Linux distributions before installing additional software.

### Step 29A: List Available Linux Distributions

**PowerShell Command:**

```powershell
wsl --list --online
```

**What this command does:**

This command requests the list of Linux distributions available for installation through WSL.

It allows me to identify supported Ubuntu versions before choosing one for my research environment.

### What I Am Looking For

| Information | Purpose |
|---|---|
| Distribution name | Identify available Ubuntu options |
| Installation identifier | Determine the correct name for a future installation command |
| Ubuntu version | Select an appropriate supported release |
| Available alternatives | Compare options before making changes |

### Why This Matters

My goal is to create a reproducible local testing environment for Calibre-Web NextGen while minimizing unnecessary downloads and preserving the target's intended runtime configuration.

Listing the available distributions does not install Ubuntu or change my existing WSL configuration.

I will also verify whether a separate Ubuntu distribution is actually necessary for the documented Docker Desktop deployment before installing it.

### Current Status

| Component | Status |
|---|---|
| Target Application | Calibre-Web NextGen 4.1.43 |
| WSL2 | Configured |
| Default WSL Version | 2 |
| Linux Distribution | Not installed |
| Available Ubuntu Versions | Awaiting results |
| Docker Desktop | Not installed |
| Application Startup | Not yet verified |

### Next Step

I will review the available Linux distributions and select an appropriate installation approach based on the project's requirements.

**Outcome:** I confirmed the existing WSL2 configuration and moved to identify available Linux distributions without installing or modifying anything.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/81941459986962842b87fee27b01d0ff0612825b/Screenshot%202026-09-17%20111635.png)


### Step 30: Install Ubuntu on Windows Using WSL2

After confirming that Ubuntu was available through Windows Subsystem for Linux, I proceeded with preparing a Linux environment for my Calibre-Web NextGen dynamic testing lab.

My objective was to follow the application's documented Windows deployment approach while avoiding unnecessary changes to its Python dependencies.

### Step 30A: Install Ubuntu

I opened PowerShell and used the following command:

```powershell
wsl --install -d Ubuntu
```

**What this command does:**

- Downloads the Ubuntu Linux distribution.
- Installs Ubuntu through Windows Subsystem for Linux.
- Uses the existing WSL configuration, which has version 2 set as the default.
- Prepares a Linux environment that can be used for development and testing.

### Step 30B: Complete Initial Ubuntu Setup

Depending on the installation process, Windows may request a restart.

If a restart is required, I must complete it before continuing.

When Ubuntu launches for the first time, it may prompt me to create a local Linux account.

Example prompts:

```text
Enter new UNIX username:
New password:
Retype new password:
```

The Ubuntu username and password are separate from my Windows credentials.

**Security consideration:** I will use a unique password and avoid including credentials in screenshots, terminal logs, or my public GitHub repository.

### Step 30C: Verify the Installation

After completing Ubuntu's initial setup, I will confirm that the distribution is registered under WSL2.

**Verification command (to run after installation):**

```powershell
wsl --list --verbose
```

**Expected result:**

```text
  NAME       STATE      VERSION
* Ubuntu     Running    2
```

The distribution may also show `Stopped` when it is not actively running. The important value is `VERSION 2`.

This is an expected example; the actual installation result still needs to be verified.

### Why This Matters

Using Ubuntu through WSL2 provides a Linux environment without requiring a separate physical computer or traditional virtual machine installation.

It also allows me to continue toward the application's documented Windows deployment method without forcing incompatible Python package versions.

However, installing Ubuntu alone does not install Docker Desktop or start Calibre-Web NextGen.

### Current Environment Status

| Component | Status |
|---|---|
| Target Application | Calibre-Web NextGen 4.1.43 |
| WSL2 | Configured |
| Default WSL Version | 2 |
| Ubuntu | Installation pending verification |
| Docker Desktop | Not yet installed |
| Python Dependency Workaround | Deferred |
| Application Startup | Not yet verified |

### Next Step

I will complete the Ubuntu installation, configure my local Linux account, and verify that Ubuntu is registered as a WSL2 distribution.

Afterward, I will assess the remaining requirements for deploying Calibre-Web NextGen through its documented container environment.

**Outcome:** I initiated the Ubuntu installation process as the next step toward building a reproducible local dynamic testing environment. Successful installation and initialization remain to be confirmed.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0c9bf4917da5bb340d50c72bba1d921a56bec6a6/Screenshot%202026-09-17%20112003.png)


### Step 31: Verify Ubuntu Installation and WSL2 Configuration

After running the Ubuntu installation command, I received confirmation that the Linux distribution had been installed successfully.

**Confirmed installation output:**

```text
Ubuntu has been installed.
Launching Ubuntu...
```

Ubuntu launched successfully, and I entered the Linux terminal as the `root` user.

I also noticed that my current working directory was under `/mnt/c/`, which represents the Windows C: drive mounted inside WSL.

### Step 31A: Return to Windows PowerShell

Before verifying the installation, I exited the Ubuntu terminal.

**Ubuntu command:**

```bash
exit
```

This returned me to my Windows PowerShell session.

### Step 31B: Verify the WSL Distribution

Once back in PowerShell, I ran:

```powershell
wsl --list --verbose
```

**What this command does:**

This command displays the Linux distributions registered with Windows Subsystem for Linux, including their current state and WSL version.

### Expected Output

```text
  NAME       STATE      VERSION
* Ubuntu     Stopped    2
```

Ubuntu may display `Running` instead of `Stopped`, depending on whether the distribution is active.

The important requirement is that Ubuntu appears with **VERSION 2**.

The actual verification output is still pending.

### Step 31C: Understand the Filesystem Layout

During the initial Ubuntu session, I observed that I was working under `/mnt/c/`.

This directory provides access to files stored on my Windows C: drive.

For future Docker configuration, I need to distinguish between the Windows filesystem and the native WSL Linux filesystem.

| Location | Purpose |
|---|---|
| `/mnt/c/` | Access to the Windows C: drive |
| `/root/` | Root user's home directory inside Linux |
| `/home/` | Standard location for regular Linux user home directories |
| Native WSL filesystem | Preferred location for Linux-based container development data |

The project's setup instructions recommend keeping Docker runtime data inside the WSL filesystem rather than under `/mnt/c/`.

I will account for this when configuring the container environment.

### Security Consideration

I initially accessed Ubuntu as `root`, which provides administrative privileges.

Although this is sufficient for the current installation checks, I will consider creating a standard Linux user before performing routine development tasks.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| WSL2 | Installed |
| Default WSL Version | 2 |
| Ubuntu | Successfully installed and launched |
| Ubuntu Account | Root |
| WSL2 Distribution Verification | Pending |
| Docker Desktop | Not yet installed |
| Application Startup | Not yet verified |

### Next Step

I will confirm that Ubuntu is registered as a WSL2 distribution by reviewing the output of `wsl --list --verbose`.

Once verified, I can continue preparing the supported container environment for Calibre-Web NextGen.

**Outcome:** Ubuntu installed and launched successfully. The next task is to verify its WSL version from Windows PowerShell.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/08ee2fbce534724ce63eff24d4ff9151a9ea72b9/Screenshot%202026-09-17%20210557.png)


### Step 32: Prepare to Install Docker Desktop for Windows

After installing Ubuntu, I verified that it was correctly configured to use WSL2.

**Confirmed PowerShell output:**

```text
  NAME      STATE     VERSION
* Ubuntu    Stopped   2
```

The `Stopped` status was normal because I had exited the Ubuntu terminal. The important result was that Ubuntu was registered as a **WSL2 distribution**.

### Step 32A: Download Docker Desktop

With WSL2 and Ubuntu configured, I was ready to proceed with the Docker Desktop installation recommended by the Calibre-Web NextGen setup guide.

**Official download page:**

https://www.docker.com/products/docker-desktop/

**Installation procedure:**

1. Open the official Docker Desktop website.
2. Select the Windows download option appropriate for my computer.
3. Download the Docker Desktop installer.
4. Launch the installer.
5. Use the WSL2-based engine option when available.
6. Complete the installation and restart Windows if requested.

### Why I Selected Docker Desktop

My earlier attempts to install Calibre-Web NextGen directly through Python encountered dependency and compiler issues.

Docker provides a way to run the application using a packaged runtime environment rather than manually compiling every Python dependency on Windows.

This approach helps preserve the target application's intended configuration for reproducible vulnerability research.

### Storage Considerations

My earlier disk-space check showed approximately 47.2 GiB available on the C: drive.

Docker Desktop, its WSL backend, downloaded container images, and application data will consume additional storage.

Before downloading application images, I will verify that Docker is functioning and review where its runtime data will be stored.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| WSL2 | Verified |
| Ubuntu | Installed |
| Ubuntu WSL Version | 2 |
| Docker Desktop | Installation pending |
| Docker Container | Not yet created |
| Application Startup | Not yet verified |

### Next Step

I will download and install Docker Desktop for Windows using the official installer.

After installation, I will verify that Docker Desktop launches successfully and that its WSL2 backend is available before downloading the Calibre-Web NextGen container image.

**Outcome:** I completed the WSL2 and Ubuntu prerequisites and advanced to preparing the Docker Desktop installation.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d495d55eaccf280dfac3b7cd4d9085d851ac8929/Screenshot%202026-09-17%20210740.png)

Select the version for your computer

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/833af0bd1d4195da937212a925d7cb4f6291fc75/Screenshot%202026-09-17%20210908.png)

Select your configurations and what for Docker to install

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5671c61cdf9112088b7621545ffa3a80c3854ad6/Screenshot%202026-09-18%20184257.png)


### Step 33: Install Docker Desktop Using the WSL2 Backend

After downloading the appropriate Docker Desktop installer for Windows, I proceeded with configuring the container runtime for my Calibre-Web NextGen dynamic testing lab.

### Step 33A: Configure the Installation

During installation, I planned to use the **WSL 2 backend** because I had already verified that Ubuntu was installed and configured as a WSL2 distribution.

My installation settings were:

- **WSL 2 backend:** Enabled
- **Hyper-V backend:** Not required for this configuration
- **Container type:** Linux containers

Using the WSL2 backend would allow Docker Desktop to run Linux containers on my Windows 11 computer.

### Step 33B: Launch Docker Desktop

After completing the installation, I would:

1. Restart Windows if prompted.
2. Launch Docker Desktop.
3. Complete any required first-launch setup.
4. Wait for Docker Desktop to initialize its backend.
5. Confirm that the Docker engine reports a running state.

### Expected Result

Docker Desktop should indicate that the Docker engine is running.

This status would confirm that the container runtime has started, although additional command-line verification would still be necessary.

### Why This Matters

My previous native Python installation attempts encountered dependency and compiler issues.

Using Docker Desktop with WSL2 provides an alternative deployment environment aligned with the project's documented Windows setup approach.

This also helps me avoid modifying the application's dependency constraints solely to make the application run.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| WSL2 | Verified |
| Ubuntu | Installed, WSL version 2 |
| Docker Desktop | Installation and startup pending confirmation |
| WSL2 Docker Backend | Selected installation approach |
| Docker Engine | Awaiting verification |
| Application Container | Not yet created |

### Next Step

I will confirm that Docker Desktop has finished installing and that its engine is running before attempting to download or launch the Calibre-Web NextGen container.

**Outcome:** I proceeded with the documented Docker Desktop installation approach using WSL2. Successful engine startup remains to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c6a94fb37d1678cae6697e1021809f3717264f5/Screenshot%202026-09-18%20190940.png)


### Step 34: Configure Docker Desktop Storage on the D: Drive

After installing Docker Desktop, I began configuring its storage location for my Calibre-Web NextGen dynamic testing environment.

My goal was to store Docker's Linux container data on the **D: drive** instead of consuming additional space on my Windows C: drive.

### Step 34A: Open Docker Desktop Settings

1. Launch Docker Desktop.
2. Locate the **Settings (⚙️)** icon near the top-right corner.
3. Click the icon to open Docker Desktop settings.

### Why This Matters

Docker stores container images, writable container layers, and volumes in its data storage location.

Because my C: drive had limited available space, I wanted to configure Docker storage before downloading the Calibre-Web NextGen container image.

Moving Docker's disk image to the D: drive can help reduce storage pressure on C:.

### Current Status

| Component | Status |
|---|---|
| WSL2 | Configured |
| Ubuntu | Installed using WSL2 |
| Docker Desktop | Installation completed, engine status to be confirmed |
| Target Storage Drive | D: |
| Docker Data Location | Not yet changed |
| Calibre-Web NextGen Container | Not yet deployed |

### Next Step

I will locate Docker Desktop's disk image storage settings and confirm the available configuration options before changing the storage location.

**Outcome:** I began configuring Docker Desktop storage to support the vulnerability research lab while minimizing disk usage on the system drive.



![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6cf3a4507f72169ef3ee949e2501fcc2488ac0d9/Screenshot%202026-09-18%20220643.png)


### Step 35: Configure Docker's Disk Image Location on the D: Drive

After opening Docker Desktop settings, I continued configuring the storage location for my Calibre-Web NextGen vulnerability research environment.

My objective was to store Docker's Linux container images and runtime data on the D: drive to preserve available storage on my Windows C: drive.

### Step 35A: Locate Docker Storage Settings

In Docker Desktop, I navigated to:

**Settings → Resources → Advanced**

I looked for the setting labeled:

**Disk image location**

This setting controls where Docker Desktop stores its Linux container disk image when using the WSL2 backend.

### Step 35B: Select the Storage Directory

I planned to use the following directory:

```text
D:\Vulnerability-Research-Docker
```

**Configuration procedure:**

1. Locate **Disk image location**.
2. Select **Browse** or the available location-change option.
3. Choose or create `D:\Vulnerability-Research-Docker`.
4. Review the selected path.
5. Apply the change using Docker Desktop's available controls.
6. Allow Docker Desktop to complete any required data migration or restart.

I will not manually move Docker's internal WSL files while Docker Desktop is running.

### Why This Matters

Docker images, containers, and volumes can consume significant disk space during application testing.

Configuring the disk image location before downloading Calibre-Web NextGen helps me manage storage usage and maintain a dedicated location for my research environment.

The selected directory is on the Windows D: drive, but Docker continues managing its Linux container filesystem internally.

### Current Environment Status

| Component | Status |
|---|---|
| WSL2 | Verified |
| Ubuntu | Installed |
| Docker Desktop | Installed; engine verification pending |
| Intended Docker Storage | `D:\Vulnerability-Research-Docker` |
| Storage Migration | Pending confirmation |
| Calibre-Web NextGen Container | Not yet deployed |

### Next Step

I will apply the Docker disk image location change and verify that Docker Desktop finishes any required restart or migration.

Before downloading the application image, I will confirm that the Docker engine is running successfully.

**Outcome:** I selected a dedicated D: drive location for Docker's Linux container storage. Successful application of the setting remains to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f05ad7084496e96abaf5f02413db0884b3394a6a/Screenshot%202026-09-18%20222320.png)


### Step 36: Apply Docker Desktop Storage Configuration

After selecting the D: drive as my preferred Docker storage location, I proceeded to verify the configuration before applying the changes.

### Step 36A: Confirm the Disk Image Location

In Docker Desktop, I navigated to:

**Settings → Resources → Advanced**

I checked that the **Disk image location** field displayed:

```text
D:\Vulnerability-Research-Docker
```

This was the directory I selected for Docker's Linux container disk image.

### Step 36B: Apply and Restart Docker Desktop

After confirming the path, I selected:

**Apply & restart**

Docker Desktop may need to move existing data and restart its backend to apply the new storage configuration.

I allowed the process to finish without manually moving or deleting Docker's internal files.

### Why This Matters

Configuring Docker storage on the D: drive helps preserve available space on my Windows C: drive.

This is especially important because my dynamic vulnerability testing environment may require multiple container images, application data, and supporting services.

### Verification Checklist

- [ ] Disk image location displays `D:\Vulnerability-Research-Docker`
- [ ] Selected **Apply & restart**
- [ ] Docker Desktop completed its restart
- [ ] Docker engine reports **Running**
- [ ] No storage migration errors appeared

### Current Status

| Component | Status |
|---|---|
| WSL2 | Verified |
| Ubuntu | Installed using WSL2 |
| Docker Desktop | Installed |
| Intended Storage Location | D: drive |
| Storage Configuration | Awaiting application confirmation |
| Docker Engine | Awaiting post-restart verification |
| Calibre-Web NextGen Container | Not yet deployed |

### Next Step

After Docker Desktop restarts, I will verify that the engine is running and that the selected storage location was retained.

**Outcome:** I reached the final confirmation stage for Docker Desktop's D: drive storage configuration. Successful migration and restart remain to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/418f6be8a966d2aa389995acb5f2a6c21bb8f7cf/Screenshot%202026-09-18%20222750.png)



### Step 37: Verify Docker Engine Health After Storage Migration

After configuring Docker Desktop to use my external Toshiba D: drive, I restarted Docker and confirmed that the application displayed **Engine running**.

Docker Desktop also displayed a disk limit of approximately **1006.85 GB**, indicating that a substantially larger storage capacity was available for the container environment.

However, this value represents the configured disk capacity or limit, not necessarily the amount of free space remaining on the physical drive.

### Step 37A: Verify Docker from PowerShell

Before downloading the Calibre-Web NextGen container image, I wanted to confirm that the Docker engine was responding correctly.

**PowerShell Command:**

```powershell
docker info --format "Docker Root Dir: {{.DockerRootDir}} | Server Version: {{.ServerVersion}}"
```

### What This Command Does

The command queries the Docker daemon and displays two properties:

- **Docker Root Dir:** The filesystem location Docker reports for its internal data storage.
- **Server Version:** The version of the running Docker Engine.

A successful response confirms that the Docker CLI can communicate with the Docker daemon.

### Expected Output

```text
Docker Root Dir: /var/lib/docker | Server Version: <installed version>
```

The actual output may differ depending on the Docker Desktop configuration.

**Important:** When using Docker Desktop with WSL2, `/var/lib/docker` is a Linux-internal path. It does not mean Docker's disk image is stored on the Windows C: drive.

The Windows disk image location must be verified separately in Docker Desktop under **Settings → Resources → Advanced**.

### Verified Findings

| Component | Status |
|---|---|
| WSL2 | Configured |
| Ubuntu | Installed, WSL version 2 |
| Docker Desktop | Installed |
| Docker Engine | Running |
| Intended Storage Location | `D:\Vulnerability-Research-Docker` |
| Reported Disk Limit | Approximately 1006.85 GB |
| Docker CLI Communication | Awaiting verification |
| Calibre-Web NextGen Image | Not yet downloaded |

### Why This Matters

Before beginning dynamic vulnerability testing, I need a stable and reproducible application environment.

Verifying Docker's engine health helps me distinguish container infrastructure problems from application-level issues that may appear during testing.

I also need to keep the external Toshiba drive connected while Docker Desktop is running because its storage configuration depends on that drive.

### Next Step

I will review the output of the `docker info` command to verify that Docker is responding successfully.

Once confirmed, I can continue preparing the Calibre-Web NextGen container deployment while preserving the target's intended application configuration.

**Outcome:** Docker Desktop successfully restarted and reported a running engine after the storage configuration change. Command-line verification remains pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/19982429774520746b6ec7fc7404586efdcc8042/Screenshot%202026-09-18%20223447.png)


### Step 38: Verify Docker Engine and Locate the Official Compose Configuration

After completing the Docker Desktop storage migration, I verified that the Docker Engine was responding correctly through Windows PowerShell.

**Confirmed Docker output:**

```text
Docker Root Dir: /var/lib/docker | Server Version: 29.8.0
```

### Step 38A: Confirm Docker Engine Health

The command successfully returned two important values:

| Property | Verified Result |
|---|---|
| Docker Root Directory | `/var/lib/docker` |
| Docker Engine Version | `29.8.0` |
| Docker Engine Communication | Successful |
| Docker Desktop Storage | `D:\Vulnerability-Research-Docker\DockerDesktopWSL` |

The `/var/lib/docker` directory represents Docker's internal Linux filesystem location.

Because I had configured Docker Desktop's WSL disk image on my external D: drive, this Linux path was consistent with my storage configuration.

**My conclusion:** Docker was operational, and I could proceed toward deploying Calibre-Web NextGen.

### Step 38B: Search for the Project's Docker Compose File

Before creating any container configuration, I wanted to determine whether the Calibre-Web NextGen repository already provided an official Docker Compose file.

I returned to my vulnerability research directory:

```text
C:\Users\eelve\Vulnerability-Research-Lab
```

**PowerShell Command:**

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen -Filter "*compose*" | Select-Object Name
```

### What This Command Does

This command searches the top level of the cloned Calibre-Web NextGen repository for files or directories containing `compose` in their names.

The results will help me identify whether the project includes a Docker Compose configuration that I can inspect and reuse.

### Why This Matters

My objective is to test Calibre-Web NextGen version `4.1.43` in an environment that closely matches its documented deployment configuration.

Using the project's own Compose configuration, if available, helps me:

- Preserve the intended application configuration.
- Avoid introducing unnecessary custom settings.
- Identify required ports, volumes, and environment variables.
- Build a reproducible environment for dynamic vulnerability testing.

Finding a Compose file does not automatically confirm that its referenced container image matches version `4.1.43`. I will verify the image version before deployment.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| Git Commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` |
| WSL2 | Verified |
| Ubuntu | Installed |
| Docker Engine | Running |
| Docker Engine Version | 29.8.0 |
| Docker Storage | D: drive |
| Official Compose File | Awaiting search results |
| Application Container | Not yet deployed |

### Next Step

I will review the repository search results to determine whether an official Docker Compose configuration is available.

If one exists, I will inspect its contents before pulling any container images or starting services.

**Outcome:** I successfully verified Docker Engine connectivity and began locating the project's own container configuration to support reproducible dynamic testing.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/15b8b1151bf07480f996ed778eb3d5770a3b68f8/Screenshot%202026-09-18%20223748.png)


### Step 39: Inspect the Official Docker Compose Configuration

After searching the Calibre-Web NextGen repository, I confirmed that it contained two Docker Compose configuration files:

- `docker-compose.yml` — Standard deployment configuration.
- `docker-compose.yml.dev` — Development configuration.

For my Lab 5 dynamic vulnerability testing environment, I decided to inspect the standard deployment configuration before starting any containers.

### Step 39A: Read the Docker Compose File

From my Windows PowerShell research directory:

```text
C:\Users\eelve\Vulnerability-Research-Lab
```

I used the following command:

```powershell
Get-Content .\targets\Calibre-Web-NextGen\docker-compose.yml
```

### What This Command Does

The command displays the contents of the project's existing Docker Compose configuration without modifying any files.

I wanted to understand how the application would run before allowing Docker to download images, create containers, or initialize persistent storage.

### Step 39B: Identify Important Configuration Settings

I planned to examine the following settings:

| Configuration | Security Research Purpose |
|---|---|
| Container image | Identify the application image and version |
| Port mappings | Determine how the web application is exposed |
| Volumes | Identify where application data will persist |
| Environment variables | Understand runtime settings |
| Restart policy | Determine container restart behavior |
| Network configuration | Identify container connectivity and exposure |

### Step 39C: Verify Persistent Storage

My Docker Desktop disk image had already been configured under:

```text
D:\Vulnerability-Research-Docker\DockerDesktopWSL
```

However, Docker's disk image location and the application's persistent data location are not necessarily the same.

Docker Compose may use:

- **Named volumes:** Managed by Docker.
- **Bind mounts:** Connected to specific host filesystem directories.

I needed to inspect the actual volume definitions before deploying the application.

This would help ensure that my test database, configuration files, and other persistent application data were stored in an appropriate location.

### Step 39D: Preserve the Research Target

My verified research target was:

```text
Application: Calibre-Web NextGen
Version: 4.1.43
Git Commit: 7e9221f455329bb3e6611b4652ac23b7f8629bb0
Branch: main
```

Before deployment, I also needed to verify whether the Compose configuration referenced a specific application version or a floating image tag such as `latest`.

Using an unverified image version could produce different application behavior and weaken the reproducibility of my vulnerability research.

### Current Environment Status

| Component | Status |
|---|---|
| WSL2 and Ubuntu | Configured |
| Docker Engine | Running |
| Docker Engine Version | 29.8.0 |
| Docker Storage | D: drive |
| Standard Compose File | Located |
| Development Compose File | Located |
| Compose Configuration Review | Pending |
| Application Image | Not yet pulled |
| Application Container | Not yet started |

### Next Step

I will review the complete contents of `docker-compose.yml` and identify its image, port mappings, volumes, and environment variables.

I will not start the application until I understand its storage configuration and confirm that the selected image corresponds to my intended research target.

**Outcome:** I located the project's official Docker Compose files and selected the standard configuration for inspection before deployment.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/74e108d580328e42a604d6e4b4c112eab1929e50/Screenshot%202026-09-18%20233929.png)


### Step 40: Create Isolated Test-Data Directories for Lab 5

After reviewing the official Calibre-Web NextGen Docker Compose configuration, I identified several important deployment settings.

**Confirmed findings:**

- The configuration references the official Calibre-Web NextGen container image.
- The application exposes port `8083`.
- Four host-side storage paths must be configured.
- The original Compose file contains placeholder paths that must be replaced before deployment.

For my Lab 5 dynamic vulnerability testing environment, I decided to create dedicated directories on my external D: drive.

This would keep my research data separate from personal files and other projects.

### Step 40A: Create the Lab 5 Directories

I opened Windows PowerShell and ran:

```powershell id="45qfdd"
New-Item -ItemType Directory -Force -Path "D:\Vulnerability-Research-Docker\calibre-lab5\config","D:\Vulnerability-Research-Docker\calibre-lab5\ingest","D:\Vulnerability-Research-Docker\calibre-lab5\library","D:\Vulnerability-Research-Docker\calibre-lab5\plugins"
```

### What This Command Does

The `New-Item` command creates four directories inside a dedicated `calibre-lab5` folder.

The `-ItemType Directory` parameter specifies that folders should be created, while `-Force` allows existing directories to be reused without an error.

### Expected Directory Structure

```text id="yfdicd"
D:\Vulnerability-Research-Docker\
└── calibre-lab5\
    ├── config\
    ├── ingest\
    ├── library\
    └── plugins\
```

### Step 40B: Understand the Directory Purposes

| Directory | Intended Purpose |
|---|---|
| `config` | Application configuration and persistent settings |
| `ingest` | Test files prepared for import |
| `library` | Isolated Calibre library data |
| `plugins` | Plugin-related test data |

The exact container mount destinations will be determined from the project's Compose configuration.

### Why This Matters for Dynamic Testing

Creating dedicated test-data directories supports a controlled security testing environment.

It allows me to:

- Keep research files separate from personal documents.
- Avoid mounting my real ebook library into the application.
- Preserve application state between container restarts.
- Document and reproduce test conditions.
- Prepare for later backup and restoration procedures.

The directories alone do not provide a complete security sandbox. Container permissions, network exposure, and volume mappings must also be reviewed before testing.

### Step 40C: Preserve the Original Target Configuration

I decided not to modify the repository's original `docker-compose.yml`.

Instead, I planned to create a separate Compose configuration specifically for Lab 5.

This would allow me to configure isolated storage paths and the intended application version without changing the original research target.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Docker Storage | External D: drive |
| Official Compose File | Inspected |
| Lab 5 Test Directories | Creation pending verification |
| Separate Lab 5 Compose File | Not yet created |
| Application Container | Not yet deployed |

### Next Step

I will confirm that all four Lab 5 directories were created successfully.

Afterward, I will prepare a separate Docker Compose file using the application's documented configuration and the isolated test-data paths.

**Outcome:** I established the directory plan for an isolated Calibre-Web NextGen testing environment without modifying the original repository.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/9d39a795375e89ea27465b07f7917ee7e484012a/Screenshot%202026-09-18%20234342.png)


### Step 41: Create a Separate Docker Compose File for Lab 5

After creating the isolated test-data directories, I confirmed that all four folders were successfully created on my external D: drive:

- `config`
- `ingest`
- `library`
- `plugins`

My next objective was to create a separate Docker Compose configuration for Lab 5 without modifying the original Calibre-Web NextGen repository.

### Step 41A: Copy the Official Compose Configuration

From my Windows PowerShell research directory:

```text
C:\Users\eelve\Vulnerability-Research-Lab
```

I used the following command:

```powershell
Copy-Item .\targets\Calibre-Web-NextGen\docker-compose.yml .\docker-compose-lab5.yml
```

**What this command does:**

The `Copy-Item` command copies the application's original `docker-compose.yml` into my main research directory and names the copy `docker-compose-lab5.yml`.

This allows me to maintain two separate configurations:

| File | Purpose |
|---|---|
| `targets\Calibre-Web-NextGen\docker-compose.yml` | Original project configuration |
| `docker-compose-lab5.yml` | Dedicated configuration for my dynamic testing environment |

### Step 41B: Verify the Copied File

After copying the configuration, I used PowerShell to verify that the new file existed.

**PowerShell Command:**

```powershell
Get-Item .\docker-compose-lab5.yml | Select-Object Name,Length
```

### Expected Result

PowerShell should display the copied filename and its size in bytes.

```text
Name                    Length
----                    ------
docker-compose-lab5.yml <file size>
```

The actual file size will be recorded after verification.

### Step 41C: Prepare for Isolated Volume Mapping

Once the copied file is verified, I will update only the Lab 5 configuration to reference my dedicated test directories.

**Planned host-side paths:**

```text
D:\Vulnerability-Research-Docker\calibre-lab5\config
D:\Vulnerability-Research-Docker\calibre-lab5\ingest
D:\Vulnerability-Research-Docker\calibre-lab5\library
D:\Vulnerability-Research-Docker\calibre-lab5\plugins
```

I will preserve the original container-side mount destinations from the project's configuration.

I will also verify the application image tag before launching the container to ensure the running target matches Calibre-Web NextGen version `4.1.43`.

### Why This Matters

Separating my Lab 5 Compose file from the original project configuration helps maintain the integrity of my vulnerability research.

It allows me to:

- Preserve the original repository configuration.
- Use isolated test data instead of personal files.
- Document environment-specific configuration changes.
- Compare my testing configuration against the original.
- Reproduce the testing environment later.

This approach also helps distinguish application behavior from changes introduced by my lab configuration.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Docker Storage | External D: drive |
| Lab 5 Test Directories | Created successfully |
| Original Compose File | Preserved |
| Lab 5 Compose Copy | Awaiting verification |
| Volume Mapping Changes | Not yet applied |
| Application Container | Not yet deployed |

### Next Step

I will verify that `docker-compose-lab5.yml` was copied successfully.

Afterward, I will edit the Lab 5 copy to reference the four isolated D: drive directories while preserving the application's original container mount destinations.

**Outcome:** I began preparing a dedicated Docker Compose configuration for controlled dynamic vulnerability testing without changing the original Calibre-Web NextGen repository.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/44c9f34633fe9a218df29da953f10dccd468c518/Screenshot%202026-09-18%20234745.png)


### Step 42: Configure Isolated Docker Volumes for Lab 5

After creating my separate Docker Compose file, I verified that it existed and had a file size of **3,507 bytes**.

**Verified file:**

```text
docker-compose-lab5.yml
Length: 3507 bytes
```

The original Calibre-Web NextGen `docker-compose.yml` remained unchanged.

My next objective was to replace the four placeholder storage paths in my Lab 5 configuration with the isolated directories I created on my external D: drive.

### Step 42A: Replace the Placeholder Volume Paths

From my PowerShell research directory, I ran:

```powershell
$compose = Get-Content .\docker-compose-lab5.yml -Raw

$compose = $compose.Replace('/path/to/config/folder','D:/Vulnerability-Research-Docker/calibre-lab5/config')

$compose = $compose.Replace('/path/to/the/folder/you/want/to/use/for/book/ingest','D:/Vulnerability-Research-Docker/calibre-lab5/ingest')

$compose = $compose.Replace('/path/to/your/calibre/library','D:/Vulnerability-Research-Docker/calibre-lab5/library')

$compose = $compose.Replace('/path/to/your/calibre/plugins/folder','D:/Vulnerability-Research-Docker/calibre-lab5/plugins')

Set-Content .\docker-compose-lab5.yml $compose
```

### What This Command Does

The PowerShell block reads the complete Lab 5 Compose file, replaces four predefined placeholder paths, and saves the updated configuration.

I used forward slashes in the Windows drive paths because Docker Compose supports this path notation.

The changes were limited to the separate Lab 5 configuration file.

### Step 42B: Verify the Volume Paths

After saving the changes, I used PowerShell to search for the new D: drive paths.

**Verification command:**

```powershell
Select-String -Path .\docker-compose-lab5.yml -Pattern "D:/Vulnerability"
```

### Expected Volume Paths

The search should identify four configured host directories:

```text
D:/Vulnerability-Research-Docker/calibre-lab5/config
D:/Vulnerability-Research-Docker/calibre-lab5/ingest
D:/Vulnerability-Research-Docker/calibre-lab5/library
D:/Vulnerability-Research-Docker/calibre-lab5/plugins
```

The exact volume mapping lines will include the container-side paths inherited from the original Compose file.

**Verification status:** Awaiting the actual PowerShell output.

### Step 42C: Understand the Security Benefits

Separating the application's persistent data into dedicated directories helps me maintain a controlled research environment.

| Directory | Intended Function |
|---|---|
| `config` | Store application settings and configuration data |
| `ingest` | Hold test files for ingestion |
| `library` | Store an isolated test ebook library |
| `plugins` | Store plugin-related test data |

This configuration reduces the risk of accidentally mixing research data with personal files.

However, bind-mounted directories remain accessible to the container according to their configured permissions. I will therefore use only non-sensitive test files in these directories.

### Step 42D: Preserve Research Integrity

I maintained the original Compose configuration inside the target repository.

My Lab 5 copy allows me to document exactly which environment-specific settings were changed.

This is important because my dynamic testing results must distinguish application vulnerabilities from issues introduced by my testing configuration.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen | Version 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Docker Storage | External D: drive |
| Lab 5 Test Directories | Created |
| Lab 5 Compose File | Created — 3,507 bytes before editing |
| Volume Path Replacement | Pending verification |
| Compose YAML Validation | Not yet performed |
| Application Container | Not yet started |

### Next Step

I will verify that all four volume paths were updated correctly.

Afterward, I will validate the Docker Compose configuration before downloading the application image or starting any containers.

**Outcome:** I prepared the Lab 5 Compose file to use isolated D: drive storage while preserving the original target configuration. Successful path replacement remains to be verified.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/277d689175a8f8b8bd190e8fede65bd7ba69eff3/Screenshot%202026-09-18%20235040.png)


### Step 43: Validate the Lab 5 Docker Compose Configuration

After updating my separate Docker Compose file, I verified that all four persistent storage mappings pointed to the dedicated Lab 5 directories on my external D: drive.

### Step 43A: Confirm the Volume Mappings

**Verified configuration:**

```yaml
volumes:
  - D:/Vulnerability-Research-Docker/calibre-lab5/config:/config
  - D:/Vulnerability-Research-Docker/calibre-lab5/ingest:/cwa-book-ingest
  - D:/Vulnerability-Research-Docker/calibre-lab5/library:/calibre-library
  - D:/Vulnerability-Research-Docker/calibre-lab5/plugins:/config/.config/calibre/plugins
```

These mappings connect the application's container directories to my isolated research directories.

| Host Directory | Container Directory |
|---|---|
| `config` | `/config` |
| `ingest` | `/cwa-book-ingest` |
| `library` | `/calibre-library` |
| `plugins` | `/config/.config/calibre/plugins` |

The additional search result was a comment referencing the configuration path, not an unexpected fifth volume mapping.

### Step 43B: Validate the Compose File

Before downloading the application image or starting a container, I needed to confirm that Docker Compose could parse and validate my configuration.

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml config --quiet
```

### What This Command Does

- `docker compose` invokes Docker's Compose functionality.
- `-f .\docker-compose-lab5.yml` specifies my separate Lab 5 configuration.
- `config` parses and validates the Compose configuration.
- `--quiet` suppresses the rendered configuration and normally produces no output when validation succeeds.

### Expected Result

If the configuration is valid, PowerShell should return to the command prompt without displaying an error.

If the configuration contains a problem, Docker Compose may display an error explaining the issue.

**Verification status:** Awaiting the actual command result.

### Why This Matters

Validating the configuration before deployment helps me identify potential problems involving:

- YAML syntax.
- Docker Compose configuration structure.
- Invalid or missing required settings.
- Variable interpolation.

A successful validation does not guarantee that the container image exists, the host directories are accessible, or the application will start correctly.

Those conditions must be verified during deployment.

### Security Research Considerations

I preserved the original Calibre-Web NextGen Compose file and made environment-specific changes only in `docker-compose-lab5.yml`.

This supports reproducible dynamic testing by keeping my storage configuration separate from the original research target.

I will also verify that the container image matches my selected target version, `4.1.43`, before starting the application.

### Current Environment Status

| Component | Status |
|---|---|
| Target Application | Calibre-Web NextGen 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Docker Storage | External D: drive |
| Isolated Lab 5 Directories | Created |
| Lab 5 Compose File | Created |
| Four Bind Mounts | Verified |
| Compose Validation | Pending |
| Application Image | Not yet downloaded |
| Application Container | Not yet started |

### Next Step

I will run Docker Compose configuration validation and review the result.

If validation succeeds, I can proceed toward the first controlled deployment after confirming the intended container image version and exposure settings.

**Outcome:** I verified all four isolated volume mappings and prepared the Lab 5 Compose configuration for validation before deployment.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6b6f2f219170b48d2779de4b67deb51304d44534/Screenshot%202026-09-18%20235559.png)


### Step 44: Perform the First Controlled Startup of Calibre-Web NextGen

After configuring my isolated Lab 5 storage directories and validating the Docker Compose file, I confirmed that the configuration passed Docker's validation check.

**Validation command:**

```powershell
docker compose -f .\docker-compose-lab5.yml config --quiet
```

The command returned to the PowerShell prompt without displaying errors.

This confirmed that Docker Compose accepted the configuration syntax and structure.

### Step 44A: Start the Application Container

With the Compose configuration validated, I proceeded toward the first controlled startup of Calibre-Web NextGen.

From my research directory:

```text
C:\Users\eelve\Vulnerability-Research-Lab
```

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml up -d
```

### What This Command Does

- `docker compose` — Uses Docker Compose to manage the application.
- `-f .\docker-compose-lab5.yml` — Selects my isolated Lab 5 configuration.
- `up` — Creates and starts the services defined in the Compose file.
- `-d` — Runs the services in detached mode, allowing PowerShell to return to the prompt.

If the required container image is not available locally, Docker will attempt to download it.

The initial startup may take several minutes.

### Step 44B: Understand the Deployment Process

During startup, Docker may perform several operations:

1. Download the required container image.
2. Create the application container.
3. Configure its network and port mappings.
4. Attach the four isolated Lab 5 storage directories.
5. Start the application process.

The expected local application port is `8083`, based on the original Compose configuration.

### Step 44C: Preserve the Testing Environment

My Lab 5 deployment uses the following dedicated host directories:

| Directory | Container Mount |
|---|---|
| `D:/Vulnerability-Research-Docker/calibre-lab5/config` | `/config` |
| `D:/Vulnerability-Research-Docker/calibre-lab5/ingest` | `/cwa-book-ingest` |
| `D:/Vulnerability-Research-Docker/calibre-lab5/library` | `/calibre-library` |
| `D:/Vulnerability-Research-Docker/calibre-lab5/plugins` | `/config/.config/calibre/plugins` |

These mappings keep my test data separate from personal documents and the original application repository.

### Step 44D: Verify the Application Before Browser Access

A successful `docker compose up -d` command does not automatically prove that the application is healthy.

Before opening the web interface, I will verify:

- Whether the container was created successfully.
- Whether its process is running.
- Whether Docker reports a health status.
- Whether application startup logs contain errors.
- Whether the deployed image corresponds to my intended research target.

**Important research consideration:** The verified source-code target is Calibre-Web NextGen version `4.1.43`. If the Compose file references a floating image tag, the downloaded container may not match that version. I must verify the deployed image before treating it as the same research target.

### Current Environment Status

| Component | Status |
|---|---|
| Target Source Version | 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Lab 5 Compose Validation | Passed |
| Isolated Storage Directories | Configured |
| Application Image | Download pending |
| Container Startup | Pending |
| Container Health | Not yet verified |
| Browser Access | Deferred |
| Dynamic Vulnerability Testing | Not yet started |

### Next Step

I will run the Docker Compose startup command and inspect its output.

After Docker completes the operation, I will check the container's running state and health before accessing the application through my browser.

**Outcome:** The Lab 5 Compose configuration passed validation, and the environment was ready for its first controlled container startup. Successful deployment and application health remain to be confirmed.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/aef2c58e63a6916759b028757fd1b2b94dca042c/Screenshot%202026-09-19%20001437.png)


### Step 45: Verify Calibre-Web NextGen Container Status

After running the Docker Compose startup command, I successfully downloaded and started the Calibre-Web NextGen container.

### Step 45A: Confirm the Initial Deployment

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml up -d
```

**Confirmed deployment results:**

```text
27/27 image layers pulled
ghcr.io/new-usemame/calibre-web-nextgen:latest — Pulled
Docker network — Created
calibre-web-nextgen — Started
```

This confirmed that Docker successfully downloaded the container image, created the required network, and started the application container.

However, a successful startup message does not guarantee that the application is fully operational.

### Step 45B: Check the Container Status

Before accessing the web interface, I needed to verify that the container remained running and that the expected application port was correctly mapped.

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml ps
```

### What This Command Does

This command displays the status of the services defined in my Lab 5 Docker Compose configuration.

I used it to examine:

- **NAME:** The container's assigned name.
- **IMAGE:** The container image being used.
- **STATUS:** Whether the container is running, restarting, or stopped.
- **PORTS:** The network ports exposed by the container.

### Expected Results

| Property | Expected Value |
|---|---|
| Container name | `calibre-web-nextgen` |
| Image | `ghcr.io/new-usemame/calibre-web-nextgen:latest` |
| Status | `Up` |
| Application port | `8083` |
| Port mapping | Host port `8083` mapped to container port `8083` |

The exact port binding and container status must be confirmed from the command output.

### Step 45C: Identify an Important Version-Control Issue

During deployment, Docker downloaded the image using the `latest` tag.

```text
ghcr.io/new-usemame/calibre-web-nextgen:latest
```

My original static-analysis target was Calibre-Web NextGen version `4.1.43`, at Git commit:

```text
7e9221f455329bb3e6611b4652ac23b7f8629bb0
```

The `latest` tag does not guarantee that the running container matches version `4.1.43`.

Before performing vulnerability testing, I must identify the actual application version and determine whether it matches the source code I previously analyzed.

This is important because differences between application versions could affect endpoint behavior, authentication controls, and the reproducibility of my findings.

### Step 45D: Maintain Controlled Testing Conditions

I will not access the application through `localhost:8083` until I verify the container status and review its startup logs.

I also need to confirm that the service is exposed only as intended for my local testing environment.

### Current Environment Status

| Component | Status |
|---|---|
| Docker Engine | Running — 29.8.0 |
| Docker Compose Validation | Passed |
| Image Download | Completed — 27/27 layers |
| Docker Network | Created |
| Application Container | Started |
| Container Running Status | Awaiting verification |
| Port 8083 Mapping | Awaiting verification |
| Container Image Tag | `latest` |
| Actual Application Version | Not yet verified |
| Application Startup Logs | Not yet reviewed |
| Browser Access | Deferred |

### Next Step

I will run the Docker Compose status command and verify that `calibre-web-nextgen` is running with the expected port mapping.

After confirming the container status, I will review its startup logs and verify the deployed application version before beginning dynamic testing.

**Outcome:** I successfully completed the first Docker deployment of Calibre-Web NextGen. The image was downloaded, the Docker network was created, and the container started. Runtime health and application version verification remain pending.


![image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ce4e4b7ae38ae119fc22f1248b92a39ccbeddca5/Screenshot%202026-09-19%20001750.png)


### Step 46: Verify Container Health and Review Startup Logs

After deploying Calibre-Web NextGen through Docker Compose, I verified that the application container was running and reported a healthy status.

### Step 46A: Confirm Container Status

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml ps
```

**Verified results:**

```text
Container: calibre-web-nextgen
Status: Up 4 minutes (healthy)
Port: 8083 → 8083/tcp
```

These results confirmed that:

- The container remained running after its initial startup.
- Docker reported the container as healthy.
- Host port `8083` was mapped to container port `8083`.

The healthy status indicated that the container's configured health check was passing. However, this did not establish that every application feature was functioning correctly.

### Step 46B: Inspect the Application Startup Logs

Before opening the application in my browser, I decided to review the latest startup messages for potential errors.

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml logs --tail=30
```

### What This Command Does

The command retrieves the most recent 30 log lines from the services defined in my Lab 5 Compose configuration.

I used it to investigate whether the application reported any startup errors, configuration issues, or initialization failures.

### Step 46C: Review the Logs for Problems

| Log Finding | What I Am Checking |
|---|---|
| Application startup | Whether the web application initialized |
| Database initialization | Whether database setup completed without errors |
| Permission errors | Whether the application can access its mounted directories |
| Missing files | Whether required configuration or library files are unavailable |
| Tracebacks | Whether Python exceptions occurred |
| Service readiness | Whether the application reports that it is ready to accept requests |

The actual findings will be documented after reviewing the command output.

### Step 46D: Maintain Version Accuracy

The deployed image currently uses:

```text
ghcr.io/new-usemame/calibre-web-nextgen:latest
```

My original static-analysis target was version `4.1.43`, commit:

```text
7e9221f455329bb3e6611b4652ac23b7f8629bb0
```

Because `latest` is a floating image tag, I still need to verify the running application's version before beginning vulnerability testing.

This will ensure that my dynamic testing results correspond to the intended research target.

### Current Environment Status

| Component | Status |
|---|---|
| Docker Engine | Running — 29.8.0 |
| Lab 5 Compose Configuration | Validated |
| Container | `calibre-web-nextgen` |
| Container Status | Healthy |
| Port Mapping | 8083 → 8083/tcp |
| Persistent Storage | Isolated D: drive directories |
| Startup Logs | Awaiting review |
| Running Application Version | Not yet verified |
| Browser Access | Not yet performed |
| Dynamic Testing Baseline | Not yet established |

### Next Step

I will inspect the final 30 lines of the container logs.

If the logs show no blocking startup errors, I will proceed toward opening the application at:

```text
http://localhost:8083
```

Before beginning security testing, I will also verify the deployed application version and establish a clean baseline using isolated test data.

**Outcome:** I confirmed that the Calibre-Web NextGen container was running with a healthy status and the expected port mapping. The final startup-log review remains pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7b3d7afb3cfafb3b87529a0cce78db1e154ed5f3/Screenshot%202026-09-19%20002112.png)


### Step 47: Open Calibre-Web NextGen and Establish the Local Testing Baseline

After confirming that the Calibre-Web NextGen container was healthy, I reviewed its startup logs to determine whether the application had initialized successfully.

### Step 47A: Review Startup Log Findings

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml logs --tail=30
```

**Confirmed findings:**

| Component | Result |
|---|---|
| Container health | Healthy |
| Gevent web server | Started on port 8083 |
| Localhost connection test | Successful |
| Book ingestion directory | `/cwa-book-ingest` recognized |
| Calibre library directory | `/calibre-library` recognized |
| Docker file monitoring | Polling mode enabled |
| Database initialization | Completed successfully |
| Database checksum/backfill | Completed successfully |
| Fatal startup errors | None observed in reviewed logs |

These findings confirmed that the application had completed its initial startup procedures without an apparent blocking error.

### Step 47B: Note the Hardcover API Token Configuration

During the log review, I observed that `HARDCOVER_TOKEN` was reported as configured.

The original Compose file contains the placeholder:

```text
your_hardcover_api_key_here
```

This placeholder is not a real API credential.

Although it did not prevent application startup, I documented it for later configuration cleanup.

### Step 47C: Open the Local Web Interface

With the container healthy and startup logs reviewed, I proceeded to open Calibre-Web NextGen in my browser.

**Local application URL:**

http://localhost:8083

I entered this address into my browser to access the application running inside Docker.

### Why This Matters

Opening the application is the next step toward establishing a functional baseline for Lab 5 dynamic vulnerability testing.

Before investigating security hypotheses, I need to verify that the application responds correctly and that its initial setup can be completed using isolated test data.

I will also confirm the running application version before comparing dynamic behavior against the source code examined during static analysis.

**Security note:** Accessing `localhost:8083` connects to the local machine. However, the Compose port binding must be inspected separately to confirm whether the service is restricted to loopback or also accessible through other network interfaces.

### Current Environment Status

| Component | Status |
|---|---|
| Target Source Version | 4.1.43 |
| Docker Engine | Running — 29.8.0 |
| Container Health | Healthy |
| Startup Logs | No fatal errors observed |
| Database Initialization | Successful |
| Local Application Port | 8083 |
| Browser Access | Pending verification |
| Running Application Version | Not yet confirmed |
| Dynamic Testing Baseline | In progress |

### Next Step

I will open `http://localhost:8083` and document the initial application screen.

Once the web interface loads, I will identify any first-run configuration requirements before proceeding with controlled security testing.

**Outcome:** I confirmed successful container initialization, database setup, and web server startup. The environment is ready for its first browser-based baseline check.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b75138a3d7fcc8f2c3df7ae9ce88155bb2bc1b2a/Screenshot_19-9-2026_105658_chatgpt.com.jpeg)


### Step 48: Verify the Login Page and Investigate Initial Administrator Credentials

After opening Calibre-Web NextGen in my browser, I confirmed that the application was accessible through my local Docker environment.

**Local URL:**

```text
http://localhost:8083
```

### Step 48A: Confirm the Authentication Redirect

When I accessed the application without an authenticated session, the server redirected my browser to:

```text
http://localhost:8083/login?next=%2F
```

The login page rendered successfully.

**Confirmed findings:**

- The application was reachable through port `8083`.
- Unauthenticated access to the home page redirected to the login endpoint.
- The login interface displayed normally.
- The Docker deployment was functioning at the web application level.

This established an initial authentication baseline for Lab 5.

However, the redirect alone does not prove that every protected endpoint enforces authentication correctly.

### Step 48B: Investigate the Initial Administrator Login

Before attempting to authenticate, I needed to determine the application's intended administrator setup procedure.

Rather than guessing usernames or passwords, I decided to inspect the container's startup logs for relevant initialization messages.

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml logs | Select-String -Pattern "admin|password|username|login|credential" -CaseSensitive:$false
```

### What This Command Does

The command retrieves the container logs and searches for messages containing:

- `admin`
- `password`
- `username`
- `login`
- `credential`

The search is case-insensitive, allowing it to identify matching messages regardless of capitalization.

### Step 48C: Identify the Intended Authentication Procedure

I will review the results to determine whether the application:

1. Generates administrator credentials during initialization.
2. Uses documented default credentials.
3. Requires an administrator account to be created during initial setup.
4. Provides another supported first-login procedure.

If the logs do not contain this information, I will consult the project's documentation rather than guessing credentials.

**Security consideration:** If the logs contain a password or authentication token, I will redact it before including screenshots or command output in my public GitHub repository.

### Why This Matters for Dynamic Vulnerability Testing

My Lab 5 research includes authentication and authorization security hypotheses.

Before testing those controls, I need a legitimate administrator account and a documented authentication baseline.

Establishing the intended login procedure will help me create controlled test accounts and distinguish expected application behavior from potential security weaknesses.

### Current Environment Status

| Component | Status |
|---|---|
| Calibre-Web NextGen Container | Healthy |
| Local Web Interface | Accessible |
| Application Port | 8083 |
| Unauthenticated Redirect | Confirmed |
| Login Page | Successfully rendered |
| Administrator Credentials | Not yet determined |
| Running Application Version | Not yet verified against 4.1.43 |
| Dynamic Testing Baseline | In progress |

### Next Step

I will inspect the startup logs for administrator initialization or credential-related messages.

After identifying the documented login procedure, I will authenticate using an authorized test account and continue establishing the Lab 5 baseline.

**Outcome:** I confirmed that Calibre-Web NextGen was accessible locally and redirected unauthenticated home-page requests to its login interface. Administrator authentication setup remains under investigation.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/014bc3660a66eb71320759ed9e494f4846a252e5/Screenshot%202026-09-19%20003135.png)


### Step 49: Investigate Default Administrator Credentials in the Source Code

After reviewing the Calibre-Web NextGen container logs, I found no evidence that the application generated or displayed initial administrator credentials.

The credential-related search returned only two configuration messages:

```text
SESSION_COOKIE_SECURE set to False (Standard/LDAP login)
Magic Link QRCode is enabled
```

These messages describe authentication-related settings, but neither identifies an initial administrator username or password.

### Step 49A: Search the Local Source Repository

Rather than guessing credentials, I decided to search the application's source code and documentation for references to its default login procedure.

**PowerShell Command:**

```powershell
Get-ChildItem .\targets\Calibre-Web-NextGen -Recurse -File -Include *.md,*.py,*.yml,*.yaml | Select-String -Pattern "admin123|default password|default username|initial password|initial login" -CaseSensitive:$false | Select-Object -First 20 Path,LineNumber,Line
```

### What This Command Does

This command recursively searches the local Calibre-Web NextGen repository.

It examines Markdown documentation, Python source files, and YAML configuration files for references to initial or default login credentials.

The results include:

- **Path:** The file containing the match.
- **LineNumber:** The location of the matching text.
- **Line:** The source-code or documentation text containing the match.

The command limits the displayed results to the first 20 matches.

### Step 49B: Investigate the Authentication Setup Process

I am looking for evidence of how a newly installed application is intended to be accessed.

| Possible Finding | Investigation Purpose |
|---|---|
| Documented default username | Identify the initial administrator account |
| Documented default password | Identify the intended first-login credential |
| Account initialization code | Understand how the initial administrator is created |
| First-run setup instructions | Determine whether account creation is required |
| No matching results | Expand the investigation to other relevant documentation or source files |

### Why This Matters

My dynamic vulnerability research requires a legitimate authentication baseline before I can investigate authorization behavior.

Using the application's documented login procedure will allow me to establish controlled test accounts without introducing unnecessary failed-login attempts.

It will also help me distinguish expected authentication behavior from potential security weaknesses.

**Security consideration:** Any actual passwords, session tokens, or other credentials discovered during setup must be excluded or redacted from public GitHub documentation.

### Current Environment Status

| Component | Status |
|---|---|
| Docker Container | Healthy |
| Local Application | Accessible at `localhost:8083` |
| Login Page | Successfully rendered |
| Unauthenticated Redirect | Confirmed |
| Generated Credentials in Logs | Not found |
| Initial Administrator Login | Under investigation |
| Source-Code Credential Search | Pending |
| Dynamic Authentication Testing | Not yet started |

### Next Step

I will review the repository search results and determine whether the application's documentation or initialization code identifies the intended first-login procedure.

If the search produces no relevant results, I will inspect additional authentication setup references rather than guessing credentials.

**Outcome:** I confirmed that the startup logs did not reveal initial credentials and moved to source-code-based investigation of the application's documented administrator login procedure.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1e4d5c085dd10daf8d88556cd765e184f9cb1b33/Screenshot%202026-09-19%20003439.png)


### Step 50: Identify and Use the Documented Default Administrator Login

After searching the Calibre-Web NextGen source repository, I identified the application's documented default administrator credentials.

This allowed me to proceed with the initial login using the intended installation procedure rather than attempting to guess credentials.

### Step 50A: Confirm Default Credentials in the Source Code

The repository search identified three supporting references:

| Source | Finding |
|---|---|
| `cps/constants.py` | Defines `DEFAULT_PASSWORD = "admin123"` |
| `cps/ub.py` | Contains logic and documentation for creating the default administrator account |
| Repository documentation | Explicitly identifies the default login as `admin/admin123` |

**Documented fresh-install credentials:**

```text
Username: admin
Password: admin123
```

These credentials were identified through the authorized local source repository.

### Step 50B: Perform the Initial Administrator Login

I returned to the Calibre-Web NextGen login page:

```text
http://localhost:8083/login?next=%2F
```

I prepared to authenticate using the documented default administrator account.

**Login procedure:**

1. Enter `admin` in the username field.
2. Enter `admin123` in the password field.
3. Select the login button.
4. Observe the application's response.

This is a documented first-login procedure within my isolated Lab 5 environment, not a password-guessing or brute-force test.

### Step 50C: Preserve the Clean Authenticated Baseline

After submitting the credentials, I will inspect the application's initial authenticated state without changing its configuration.

At this stage, I will not:

- Change the administrator password.
- Create additional user accounts.
- Modify application settings.
- Upload or import books.
- Begin vulnerability testing.

Preserving the initial state will allow me to document the application's default behavior before introducing controlled test data or configuration changes.

### Step 50D: Document the Security Significance

The presence of documented default administrator credentials is relevant to application security because unchanged default credentials can create unauthorized-access risks when an application is exposed to untrusted networks.

However, the existence of default credentials in source code does not, by itself, confirm an exploitable vulnerability in this deployment.

My immediate objective is to establish the authorized authenticated baseline, not to classify the default login as a security finding.

### Current Environment Status

| Component | Status |
|---|---|
| Docker Container | Healthy |
| Local Web Interface | Accessible |
| Login Page | Confirmed |
| Default Username | `admin` |
| Default Password | `admin123` |
| Credential Source | Local repository |
| Initial Administrator Login | Awaiting confirmation |
| Application Configuration | Unmodified |
| Dynamic Testing | Not yet started |

### Next Step

I will sign in using the documented administrator credentials and record the first authenticated page displayed by Calibre-Web NextGen.

I will not modify accounts, passwords, or settings until the authenticated baseline has been captured.

**Outcome:** I identified the documented default administrator account through the application's source code and established a controlled procedure for the first authenticated login.


![Image alt](Screenshot_19-9-2026_04441_localhost.jpeg)


### Step 51: Establish the Initial Authenticated Baseline and Verify the Empty Library

After identifying the documented default administrator credentials, I successfully authenticated to my local Calibre-Web NextGen installation.

The application loaded normally, confirming that the initial administrator login worked.

### Step 51A: Document the Authenticated Baseline

**Application URL:**

```text
http://localhost:8083
```

**Confirmed observations:**

| Component | Observed Result |
|---|---|
| Administrator login | Successful |
| Authenticated session | Established |
| Application interface | Classic Calibre-Web NextGen |
| Library book count | Books (0) |
| Visible library | Empty |
| Application functionality | Initial authenticated page loaded |
| User and application settings | Unmodified |

I saved a screenshot of the authenticated interface as evidence for **Lab 5 — Step 1: Initial Authenticated Baseline**.

This screenshot establishes the application's starting condition before adding test data, creating additional users, or performing authorization tests.

### Step 51B: Verify the Library Directory on Disk

Although the web interface displayed zero books, I also wanted to verify whether the host directory mapped to the Calibre library contained any files.

Without changing anything in the application, I returned to PowerShell.

**PowerShell Command:**

```powershell
Get-ChildItem "D:\Vulnerability-Research-Docker\calibre-lab5\library" -Force
```

### What This Command Does

- `Get-ChildItem` lists files and directories at the specified location.
- The directory path points to the isolated Calibre library used by my Lab 5 container.
- `-Force` includes hidden items that might otherwise be omitted.

This is a read-only verification command. It does not create, delete, or modify files.

### Step 51C: Interpret the Results

**Expected result:**

```text
No output
```

If the command returns no items and no errors, the mapped host library directory is empty.

If files or directories appear, I will inspect them before adding test data.

The directory verification result is still pending.

### Step 51D: Prepare for Candidate #1

My first dynamic-testing candidate concerns object-level authorization in the read-status endpoint:

```text
/ajax/toggleread/<book_id>
```

Testing this hypothesis requires a controlled environment containing at least one book object.

Later, I will also need separate test accounts with defined permissions and visibility conditions.

The purpose is to determine whether the application correctly enforces authorization when different authenticated users interact with book objects.

At this stage, I have not confirmed an authorization vulnerability.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Docker container | Healthy |
| Administrator authentication | Successful |
| Authenticated baseline screenshot | Captured |
| Books displayed | 0 |
| Host library directory | Awaiting verification |
| Test book | Not yet added |
| Additional test users | Not yet created |
| Candidate #1 | Not yet tested |

### Next Step

I will verify that the isolated Calibre library directory is empty.

Once confirmed, I will prepare a harmless test book and establish the controlled accounts required for the first object-level authorization assessment.

**Outcome:** I successfully established the initial authenticated administrator baseline. The application displayed an empty library with zero books, and no configuration changes were made. Disk-level library verification remains pending.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/3479ba64817e766faa41ed0d9272bfdf5d1ad9c9/Screenshot%202026-09-19%20110903.png)


### Step 52: Verify the Clean Library and Create Test Book #1

After establishing the authenticated administrator baseline, I inspected the Calibre library directory to verify that no books had been imported.

### Step 52A: Confirm the Library Database Files

**PowerShell Command:**

```powershell
Get-ChildItem "D:\Vulnerability-Research-Docker\calibre-lab5\library" -Force
```

**Observed files:**

```text
metadata.db
metadata.db-shm
metadata.db-wal
```

These files are associated with the application's SQLite-based Calibre library database.

Their presence does not mean the library contains books.

The authenticated web interface still displayed:

```text
Books (0)
```

No book files or book directories were observed.

**Result:** The library had been initialized, but no books had been imported. This established a clean starting point for controlled testing.

### Step 52B: Create a Harmless Test Book

To prepare for dynamic authorization testing, I selected a simple text file containing only synthetic research content.

Using original test content avoids introducing copyrighted material, personal information, or sensitive data into the lab.

**PowerShell Command:**

```powershell
Set-Content "D:\Vulnerability-Research-Docker\calibre-lab5\ingest\Lab5-Test-Book-01.txt" "Lab 5 authorized vulnerability research test book. No real user data."
```

This command creates a text file in the isolated ingestion directory configured for my Docker container.

**Test file details:**

| Property | Value |
|---|---|
| Filename | `Lab5-Test-Book-01.txt` |
| Format | Plain text |
| Content | Synthetic research text |
| Host location | `D:\Vulnerability-Research-Docker\calibre-lab5\ingest` |
| Container location | `/cwa-book-ingest` |
| Purpose | Create a controlled book object for authorization testing |

The file contains:

```text
Lab 5 authorized vulnerability research test book. No real user data.
```

### Step 52C: Check Whether the Book Is Imported

After creating the test file, I will refresh the authenticated Calibre-Web NextGen interface at:

```text
http://localhost:8083
```

I will inspect the displayed book count.

**Expected result:**

```text
Before: Books (0)
After:  Books (1)
```

This result would indicate that the ingestion process successfully created a book entry visible in the application.

The import result has not yet been confirmed.

If the count remains at zero, I will inspect the ingestion-related container logs rather than assuming that the import succeeded.

### Step 52D: Prepare for Object-Level Authorization Testing

Candidate #1 concerns the read-status endpoint:

```text
/ajax/toggleread/<book_id>
```

A successfully imported test book will provide a controlled object for examining whether read-status changes are properly authorized.

Before testing this endpoint, I will need to identify the book's object ID and establish separate accounts with defined access permissions.

No authorization weakness has been demonstrated at this stage.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Administrator authentication | Confirmed |
| Initial authenticated baseline | Captured |
| Library database | Initialized |
| Existing books | 0 |
| Test book file creation | Next action |
| Test book ingestion | Pending |
| Additional test accounts | Not yet created |
| Candidate #1 authorization testing | Not yet started |

### Next Step

I will create `Lab5-Test-Book-01.txt` in the isolated ingestion directory and refresh the application.

I will then compare the displayed book count against the clean baseline.

**Outcome:** I confirmed that the library contained only database files and no imported books. The environment was ready for the creation of its first harmless test book.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f2865bc249a59627ab1cc539bd4ea1e2935499f9/Screenshot%202026-09-19%20012052.png)


### Step 53: Investigate Why the Test Book Was Not Imported

After creating my first synthetic test book, I refreshed the authenticated Calibre-Web NextGen interface to determine whether the application automatically imported the file.

**Observed result:**

```text
Books (0)
```

The book count remained unchanged, indicating that the test file had not appeared in the application library.

### Step 53A: Review the Test File

I previously created the following file:

```text
D:\Vulnerability-Research-Docker\calibre-lab5\ingest\Lab5-Test-Book-01.txt
```

The file contained only harmless, synthetic research data:

```text
Lab 5 authorized vulnerability research test book. No real user data.
```

The ingestion directory was mapped to the container at:

```text
/cwa-book-ingest
```

Although the file was created in the designated ingestion directory, its presence there does not guarantee that the application supports automatic import of plain-text files.

### Step 53B: Search the Container Logs for Ingestion Activity

Rather than modifying application settings or trying additional file formats, I decided to investigate the container logs.

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml logs --since=5m | Select-String -Pattern "Lab5|ingest|txt|error|unsupported|failed" -CaseSensitive:$false
```

### What This Command Does

- `docker compose logs` retrieves container log messages.
- `--since=5m` limits the search to the previous five minutes.
- `Select-String` filters the output for ingestion-related messages.
- `-CaseSensitive:$false` performs a case-insensitive search.

The search looks for references to the test filename, ingestion activity, unsupported formats, and possible processing failures.

### Step 53C: Interpret the Findings

| Possible Log Result | Meaning |
|---|---|
| Test filename detected | The ingestion process noticed the file |
| Unsupported format | The ingestion process may reject `.txt` files |
| Import failure | The application attempted processing but encountered an error |
| Successful import message | Further verification is needed to determine why the book is not visible |
| No matching messages | Additional investigation of file visibility, ingestion configuration, or monitoring behavior is needed |

The command output is still pending. I will not assume that the `.txt` format is unsupported without supporting evidence.

### Step 53D: Preserve the Testing Baseline

At this stage, I will avoid changing application settings, uploading additional files, or modifying the Docker configuration.

The objective is to determine why the expected ingestion behavior did not occur before making any corrective changes.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Docker container | Healthy |
| Administrator login | Successful |
| Library database | Initialized |
| Test file creation | Command executed |
| Web interface book count | Books (0) |
| Test book ingestion | Not observed |
| Ingestion log investigation | Pending |
| Candidate #1 authorization testing | Not started |

### Next Step

I will inspect the recent container logs for ingestion-related activity.

Based on the results, I will determine whether the issue involves file-format support, file monitoring, container access, or another documented ingestion requirement.

**Outcome:** The first test book did not appear in the application library. I preserved the environment and began a targeted log investigation rather than making unverified configuration changes.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b113c2d535a9a9ce9f51aeb5ba425914a04a88ab/Screenshot%202026-09-19%20112203.png)


### Step 54: Confirm Successful TXT-to-EPUB Conversion and Verify Book Import

After the initial test book failed to appear immediately in the Calibre-Web NextGen interface, I investigated the container logs to determine whether the ingestion process had detected the file.

### Step 54A: Review the Ingestion Logs

**PowerShell Command:**

```powershell
docker compose -f .\docker-compose-lab5.yml logs --since=5m | Select-String -Pattern "Lab5|ingest|txt|error|unsupported|failed" -CaseSensitive:$false
```

The logs confirmed that the application detected my synthetic test file and initiated the conversion process.

**Confirmed log findings:**

1. New file detected.
2. Conversion started from TXT to EPUB.
3. EPUB output successfully written.
4. Conversion completed.

**Observed conversion duration:** Approximately 73 seconds.

This explained why the test book did not immediately appear after I created the original text file.

### Step 54B: Investigate Fontconfig Warnings

During conversion, the logs also displayed repeated Fontconfig warnings involving cache directories.

These warnings did not prevent the application from completing the TXT-to-EPUB conversion.

I documented them as non-blocking observations rather than treating them as confirmed application failures.

### Step 54C: Refresh the Application

After confirming that the conversion completed, I returned to the authenticated Calibre-Web NextGen interface.

**Application URL:**

http://localhost:8083

I refreshed the page to determine whether the converted book had been added to the library.

**Expected result:**

| Measurement | Value |
|---|---|
| Initial book count | Books (0) |
| Test files submitted | 1 |
| Conversion status | Completed |
| Expected updated count | Books (1) |
| Actual updated count | Awaiting confirmation |

I did not add another book or modify any application settings.

### Step 54D: Relevance to Candidate #1

The purpose of importing this synthetic book is to create a controlled object for testing the read-status authorization endpoint:

```text
/ajax/toggleread/<book_id>
```

Once the book appears in the library, I can begin documenting its identity and preparing separate user accounts with defined access permissions.

A successful conversion does not, by itself, prove that the book is visible in the application or ready for authorization testing.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Docker container | Healthy |
| Administrator authentication | Successful |
| Synthetic TXT file | Detected |
| TXT-to-EPUB conversion | Completed |
| Conversion duration | Approximately 73 seconds |
| Fontconfig warnings | Non-blocking |
| Updated book count | Awaiting verification |
| Additional test accounts | Not yet created |
| Candidate #1 | Not yet tested |

### Next Step

I will refresh the authenticated application and check whether the book count has changed from `Books (0)` to `Books (1)`.

If the book appears, I will document its details before preparing the controlled authorization tests.

**Outcome:** The container logs confirmed successful detection and conversion of the synthetic TXT file into EPUB format. Final verification of the book's appearance in the application library remains pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/54ef835a347727a2ccce7c67dd11a3a98622e600/Screenshot%202026-09-19%20012720.png)


### Step 55: Verify Successful Book Ingestion and Locate User Management

After completing the TXT-to-EPUB conversion, I refreshed the authenticated Calibre-Web NextGen interface.

The application successfully displayed my first controlled test book.

### Step 55A: Confirm the Imported Test Book

**Observed results:**

| Component | Verified Result |
|---|---|
| Application | Calibre-Web NextGen |
| Administrator session | Authenticated |
| Initial library count | Books (0) |
| Updated library count | Books (1) |
| Imported book | `Lab5-Test-Book-01` |
| TXT-to-EPUB conversion | Successful |
| Library visibility | Confirmed |

The screenshot confirmed that the book was successfully imported and appeared in the local library.

I preserved this screenshot as evidence of the completed ingestion process.

### Step 55B: Prepare for Candidate #1 — Object-Level Authorization

My first security hypothesis involves the read-status endpoint:

```text
/ajax/toggleread/<book_id>
```

The purpose of this assessment is to determine whether the application correctly enforces object-level authorization when users attempt to modify a book's read status.

To conduct this test, I need:

1. An authenticated administrator account.
2. A non-administrator test account.
3. A controlled book object.
4. Defined permissions and visibility conditions.
5. A documented comparison of authorized and unauthorized requests.

The administrator account and controlled book are now available.

### Step 55C: Locate User Management

While authenticated as the administrator, I will locate the application's user-management interface.

**Navigation procedure:**

1. Open `http://localhost:8083`.
2. Locate the wrench/tools icon near the upper-right corner.
3. Click the icon to inspect the available administration options.
4. Look for `Admin`, `User Management`, `Edit Users`, or a similarly named option.

At this stage, I will only identify the user-management interface.

I will not create accounts, modify permissions, or change application settings until the available controls have been reviewed.

### Step 55D: Preserve the Controlled Baseline

| Lab Component | Status |
|---|---|
| Docker container | Healthy |
| Administrator login | Confirmed |
| Isolated database and library | Operational |
| Controlled test book | Imported |
| Books displayed | 1 |
| Non-admin test user | Not yet created |
| User-management interface | Awaiting inspection |
| Candidate #1 testing | Not started |

### Next Step

I will open the administrator tools menu and identify the user-management option.

Once located, I will inspect the account creation controls before configuring a non-administrator test account.

**Outcome:** I successfully verified the complete ingestion workflow and established one controlled book object for dynamic authorization testing. The next phase is preparing separate user accounts to evaluate Candidate #1.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/128d3f49f65122f0a908f4ef71d51f6ecef0b201/Screenshot%202026-09-19%20120155.png)


### Step 56: Begin Creating a Non-Administrator Test User

After locating the User Management section in Calibre-Web NextGen, I opened the **Add New User** form.

The purpose of this account is to establish a standard-user baseline for Candidate #1, which concerns object-level authorization in the read-status endpoint.

### Step 56A: Configure the Test Username

In the new-user form, I entered the following username:

```text
lab5-user-a
```

This account will be used exclusively for authorized testing within my isolated Lab 5 environment.

### Step 56B: Inspect the Remaining Account Settings

Before saving the account, I will scroll down to review the available fields and permissions.

I need to identify:

- Email address requirements
- Password and password-confirmation fields
- Administrator privilege settings
- Standard-user permissions
- Book and library visibility controls

I will ensure that the account does not receive administrative privileges.

### Step 56C: Preserve the Authorization Baseline

For Candidate #1, I need to compare how an administrator and a standard user interact with the same controlled book object.

The account's permissions must be documented before testing so that any differences in behavior can be evaluated accurately.

**Current status:**

| Component | Status |
|---|---|
| Administrator account | Authenticated and working |
| Test book | `Lab5-Test-Book-01` |
| Library count | Books (1) |
| New username | `lab5-user-a` |
| Email and password | Not yet configured |
| User permissions | Awaiting inspection |
| Account creation | Not yet submitted |
| Candidate #1 testing | Not started |

### Next Step

I will scroll down in the Add New User form and inspect the remaining account fields and permission options.

I will not save the account until its intended non-administrator access level has been verified.

**Outcome:** I began configuring a dedicated standard-user account for controlled object-level authorization testing. The account has not yet been created.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/3e4b7826a1539cdc363210144db84a9babbf80be/Screenshot%202026-09-19%20120401.png)


### Step 57: Configure the Lab 5 Standard-User Password

After entering the username `lab5-user-a`, I proceeded to configure a password for the controlled test account.

This account will be used to evaluate Candidate #1, which concerns object-level authorization in the read-status endpoint.

### Step 57A: Set the Test Account Password

**Account configuration:**

```text
Username: lab5-user-a
Password: Lab5UserA!2026
Account Type: Standard User (intended)
```

I selected this password for the isolated local research environment, subject to the application's password requirements.

The password is intended only for this disposable lab account and must not be reused for personal or production accounts.

### Step 57B: Preserve Standard-User Permissions

Before creating the account, I will verify that:

- Administrator privileges are disabled.
- The account has only the standard permissions needed for testing.
- Book visibility and access options are documented.
- No unrelated application settings are modified.

### Step 57C: Maintain the Testing Baseline

| Component | Status |
|---|---|
| Administrator account | Working |
| Controlled test book | `Lab5-Test-Book-01` |
| Library count | Books (1) |
| Standard username | `lab5-user-a` |
| Lab password | Selected |
| Permission review | Pending |
| Account creation | Not yet submitted |
| Candidate #1 testing | Not started |

### Next Step

I will enter the lab password in the appropriate field and review the remaining permissions before saving the account.

**Outcome:** I selected a dedicated password for the first non-administrator test account. Account creation and permission verification remain pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/37e6f77e865028b1c283cecba7adabe43312e260/Screenshot%202026-09-19%20120619.png)


### Step 58: Configure and Save the Low-Privilege Test Account

After entering the username and lab-only password for `lab5-user-a`, I reviewed the available user permissions in Calibre-Web NextGen.

The purpose of this account is to establish a controlled, low-privilege user for **Candidate #1 — Read-Status Object Authorization**.

### Step 58A: Configure User Permissions

The permission settings initially appeared unchecked.

I selected only the permission required for basic eBook viewing.

**Intended account configuration:**

| Permission | Setting |
|---|---|
| Allow eBook Viewer | Enabled |
| Admin User | Disabled |
| Upload | Disabled |
| Edit | Disabled |
| Download | Disabled |
| Public Shelf | Disabled |
| All other optional permissions | Disabled |

This configuration is intended to prevent the test account from receiving unnecessary administrative or content-management privileges.

### Step 58B: Save the Test Account

After reviewing the permission settings, I proceeded to save the new user.

**Account details:**

```text
Username: lab5-user-a
Account Type: Low-privilege test user
Enabled Permission: Allow eBook Viewer
Administrative Access: Disabled
```

The account creation result must be confirmed after selecting **SAVE**.

### Step 58C: Explain the Authorization Test Design

Candidate #1 concerns the following endpoint:

```text
/ajax/toggleread/<book_id>
```

The goal is to determine whether the application enforces appropriate authorization when a low-privilege user attempts to change a book's read status.

The test will compare expected behavior with actual behavior using controlled accounts and the synthetic book `Lab5-Test-Book-01`.

Importantly, disabling administrative privileges does not automatically mean the user is prohibited from changing their own read status. I will establish the intended permissions and object-access rules before classifying any behavior as a vulnerability.

### Step 58D: Preserve the Account Baseline

| Lab Component | Status |
|---|---|
| Administrator account | Working |
| Controlled book | `Lab5-Test-Book-01` |
| Library count | Books (1) |
| Standard test username | `lab5-user-a` |
| eBook Viewer permission | Selected |
| Administrator permission | Unchecked |
| Other optional permissions | Unchecked |
| Account creation | Awaiting save confirmation |
| Candidate #1 testing | Not started |

### Next Step

I will verify that `lab5-user-a` appears in User Management with the intended permissions.

Once confirmed, I will establish its authenticated baseline before testing the read-status endpoint.

**Outcome:** I configured a dedicated low-privilege account with only eBook Viewer permission selected. Successful account creation and the saved permissions remain to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/98172fbee65c32c1587e133059c0194dcf3b7b88/Screenshot%202026-09-19%20120744.png)


### Step 59: Create Test Book #2 for Authorization Boundary Testing

After preparing the first low-privilege test account, I moved to the next stage of Lab 5: creating a second controlled book object.

The purpose of this book is to support **Candidate #1 — Read-Status Object Authorization**, allowing me to compare access and behavior across multiple book objects.

### Step 59A: Create the Second Test Book

From PowerShell, I prepared the following command:

```powershell
Set-Content "D:\Vulnerability-Research-Docker\calibre-lab5\ingest\Lab5-Test-Book-02.txt" "Lab 5 authorization boundary test book. No real user data."
```

**Test book details:**

| Property | Value |
|---|---|
| Filename | `Lab5-Test-Book-02.txt` |
| Format | Plain text |
| Content | Synthetic authorization test data |
| Host directory | `D:\Vulnerability-Research-Docker\calibre-lab5\ingest` |
| Container directory | `/cwa-book-ingest` |
| Purpose | Controlled object-level authorization testing |

The file contains:

```text
Lab 5 authorization boundary test book. No real user data.
```

### Step 59B: Allow the Ingestion Process to Complete

During the first book import, Calibre-Web NextGen required approximately 73 seconds to convert the TXT file into EPUB format.

I will allow the ingestion process to complete before evaluating the second book.

**Expected application state:**

| Measurement | Value |
|---|---|
| Existing book count | Books (1) |
| New test file | `Lab5-Test-Book-02.txt` |
| Expected conversion | TXT → EPUB |
| Expected final book count | Books (2) |
| Actual import result | Pending verification |

### Step 59C: Prepare for Candidate #1

With two controlled books, I can later establish different visibility or access conditions and compare how the application handles read-status requests.

The relevant endpoint is:

```text
/ajax/toggleread/<book_id>
```

Before testing, I will document which users are authorized to access each book and what read-status operations are permitted.

No authorization vulnerability has been confirmed.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Administrator authentication | Confirmed |
| Test Book #1 | Imported |
| Test Book #2 | Awaiting creation |
| Low-privilege account | Configuration prepared; save verification pending |
| Expected library count | Books (2) |
| Candidate #1 | Preparation phase |

### Next Step

I will create the second synthetic text file, allow the ingestion process to run, and refresh the Calibre-Web NextGen interface.

**Outcome:** I prepared a second harmless test book to support controlled object-level authorization testing. Successful ingestion remains to be verified.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/c70731f3d8b5bf51c406cfece9da955cda585999/Screenshot%202026-09-19%20125022.png) 


### Step 60: Verify Ingestion of the Second Test Book

After creating `Lab5-Test-Book-02.txt` in the isolated ingestion directory, I allowed Calibre-Web NextGen approximately 1–2 minutes to detect and process the file.

### Step 60A: Refresh the Books Page

I returned to the authenticated application at:

http://localhost:8083

I refreshed the Books page to check whether the second test book had been imported successfully.

### Step 60B: Verify the Expected Library Contents

The expected library contents are:

```text
Lab5-Test-Book-01
Lab5-Test-Book-02
```

| Verification | Expected Result |
|---|---|
| Total books | Books (2) |
| Test Book #1 | Visible |
| Test Book #2 | Visible |
| Second book conversion | Completed |
| Test data | Synthetic only |

**Verification status:** Awaiting confirmation from the refreshed Books page.

If the library still displays `Books (1)`, I will review the ingestion logs before making any additional changes.

### Step 60C: Prepare the Authorization Test Baseline

Once both books are visible, I will have two controlled objects available for Candidate #1.

The next phase will involve establishing different book-access conditions for the low-privilege test account, `lab5-user-a`.

This will allow me to investigate whether read-status operations respect the application's intended object-level authorization rules.

### Next Step

Confirm that both test books appear in the library and capture a screenshot showing `Books (2)`.

**Outcome:** The second test book has been submitted for ingestion. Successful import and visibility of both books remain to be confirmed.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/e057cb0edbb829026d2d0b9a08faf98c9c3dfb61/Screenshot%202026-09-19%20125704.png)


### Step 61: Identify Book ID 3 and Configure a Restricted Test Object

After importing the second synthetic test book, I confirmed that Calibre-Web NextGen displayed both books.

**Verified library contents:**

```text
Books (2)

Lab5-Test-Book-01
Lab5-Test-Book-02
```

### Step 61A: Establish the Authorization Test Design

For Candidate #1 — Read-Status Object Authorization, I need two book objects with different visibility conditions.

| Test Object | Intended Access |
|---|---|
| Lab5-Test-Book-01 | Visible to `lab5-user-a` |
| Lab5-Test-Book-02 | Restricted from `lab5-user-a` |

This arrangement will help determine whether the application enforces object-level authorization when a user attempts to modify the read status of a book that is not available to their account.

### Step 61B: Identify Book 2

While authenticated as the administrator, I opened:

```text
Lab5-Test-Book-02
```

The application's book details identified this object as:

```text
Book ID: 3
```

I recorded this identifier because it will be relevant when examining the read-status endpoint:

```text
/ajax/toggleread/3
```

No request to this endpoint has been tested yet.

### Step 61C: Add a Restriction Tag

The book details interface displayed an **+ Add tag** option.

I selected this option and prepared the following tag:

```text
lab5-restricted
```

The intended procedure is:

1. Open `Lab5-Test-Book-02` while authenticated as administrator.
2. Select **+ Add tag**.
3. Enter `lab5-restricted`.
4. Apply or save the tag if prompted.
5. Confirm that the tag appears on Book 2.

The tag's successful application remains to be verified.

### Step 61D: Understand the Authorization Boundary

Adding a tag does not automatically establish an access restriction.

After confirming that the tag is saved, I will inspect the application's user settings to determine whether Calibre-Web NextGen supports excluding tagged books from a specific user's library.

The intended configuration is:

- Administrator: Can access Book ID 3.
- `lab5-user-a`: Can access Book 1.
- `lab5-user-a`: Cannot access Book ID 3 through normal authorized application functionality.

I must verify the restriction using the low-privilege account before testing the read-status endpoint.

A book being absent from the normal library view does not, by itself, prove that server-side access is denied.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Administrator authentication | Confirmed |
| Library count | Books (2) |
| Book 1 | Imported |
| Book 2 | Imported |
| Book 2 ID | 3 |
| Restriction tag | Awaiting save confirmation |
| User-specific visibility restriction | Not yet configured |
| Candidate #1 authorization boundary | Not yet established |

### Next Step

I will apply the `lab5-restricted` tag to Book ID 3 and verify that the tag appears in its details.

Afterward, I will inspect the available user-specific tag restrictions for `lab5-user-a`.

**Outcome:** I established a two-book test dataset, identified Book 2 as object ID 3, and selected a tag-based approach for creating a controlled authorization boundary. The restriction has not yet been verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/36b245b33ed530724ec156b3d2405c0842d93885/Screenshot%202026-09-19%20131342.png)


### Step 62: Open User Settings to Configure the Book Visibility Restriction

After applying the `lab5-restricted` tag to `Lab5-Test-Book-02`, I confirmed that the tag appeared on the book.

The book's identifier is:

```text
Book ID: 3
Tag: lab5-restricted
```

### Step 62A: Navigate to User Management

While authenticated as the administrator, I navigated to:

**Admin Settings → Users → Edit Users**

I then selected the low-privilege test account:

```text
lab5-user-a
```

### Step 62B: Prepare the User-Specific Restriction

The objective is to configure `lab5-user-a` so that books tagged `lab5-restricted` are excluded from that user's permitted library access.

**Intended authorization boundary:**

| Book | Admin | lab5-user-a |
|---|---|---|
| Lab5-Test-Book-01 | Allowed | Allowed |
| Lab5-Test-Book-02 (ID 3) | Allowed | Restricted |

This configuration will provide a controlled comparison for Candidate #1 — Read-Status Object Authorization.

### Step 62C: Inspect the Available Restriction Controls

Before changing any settings, I will inspect the user-editing interface for options such as:

- Allowed or denied tags
- Excluded tags
- Book visibility restrictions
- Library access filters

I will not assume that a tag-based visibility filter provides server-side authorization until its behavior is verified.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Library count | Books (2) |
| Book 2 ID | 3 |
| Book 2 restriction tag | Confirmed |
| Low-privilege account | `lab5-user-a` |
| User-specific tag exclusion | Not yet configured |
| Authorization boundary | Awaiting verification |
| Candidate #1 testing | Not started |

### Next Step

I will open the settings for `lab5-user-a` and identify the supported tag-restriction controls before modifying the account.

**Outcome:** Book ID 3 has been tagged for the authorization test, and the next action is to inspect the low-privilege user's visibility settings.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/40513bbff67ce88c966c124f7ecd6753ace437ea/Screenshot%202026-09-19%20132519.png)


### Step 63: Locate User-Specific Book Visibility Restrictions

After tagging `Lab5-Test-Book-02` with `lab5-restricted`, I continued configuring the authorization boundary for Candidate #1.

While authenticated as administrator, I opened:

**Admin Settings → Users → Edit Users → lab5-user-a**

### Step 63A: Inspect Content Restriction Settings

I scrolled through the user's settings to locate controls related to:

- Allowed tags
- Denied or excluded tags
- Content restrictions
- Book visibility
- Library access filters

I did not modify any settings during this inspection.

### Step 63B: Define the Expected Authorization Boundary

My goal is to establish the following conditions:

| Book | Object ID | Expected Access for lab5-user-a |
|---|---|---|
| Lab5-Test-Book-01 | To be confirmed | Allowed |
| Lab5-Test-Book-02 | 3 | Denied |

Book ID 3 has already been assigned the tag:

```text
lab5-restricted
```

If the application supports user-specific excluded tags, I will configure `lab5-user-a` to exclude `lab5-restricted`.

### Step 63C: Verify Server-Side Authorization

Hiding a book from the library interface is not sufficient evidence that access is restricted.

Before testing Candidate #1, I must establish that:

1. The administrator can access Book ID 3.
2. `lab5-user-a` can access Book 1.
3. Book ID 3 is excluded from `lab5-user-a`'s permitted library.
4. The application's intended authorization rules prohibit the low-privilege user from modifying Book ID 3.

Only then can I evaluate the read-status endpoint:

```text
/ajax/toggleread/3
```

The test will determine whether the server enforces the intended restriction when the object ID is supplied directly.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Test books | 2 |
| Restricted object | Book ID 3 |
| Restriction tag | `lab5-restricted` |
| Test account | `lab5-user-a` |
| User-specific exclusion | Pending |
| Server-side restriction | Not yet verified |
| Candidate #1 testing | Not started |

### Next Step

I will inspect the available visibility and restriction controls in the `lab5-user-a` settings.

Once I identify the appropriate setting, I will configure the tag exclusion and verify the resulting access behavior.

**Outcome:** I defined the intended authorization boundary and began locating the application-supported user restriction settings. No authorization vulnerability has been confirmed.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/878c757a9204b13c908ac61d1eb0c7c053c2e31f/Screenshot_19-9-2026_132814_localhost%20Settings.jpeg)


### Step 64: Configure the Denied Tag for the Low-Privilege User

After locating the **Allowed/Denied Tags** section in the settings for `lab5-user-a`, I proceeded to configure the visibility restriction for Candidate #1 — Read-Status Object Authorization.

### Step 64A: Add the Restriction Tag

While authenticated as administrator, I navigated to:

**Admin Settings → Users → Edit Users → lab5-user-a**

I selected:

**ADD ALLOWED/DENIED TAGS**

I then entered the following tag:

```text
lab5-restricted
```

### Step 64B: Configure the Tag as Denied

The objective is to place `lab5-restricted` in the user's **Denied Tags** configuration, not Allowed Tags.

This distinction is important because Book ID 3 already has that tag.

**Intended configuration:**

| Setting | Value |
|---|---|
| Username | `lab5-user-a` |
| Account privileges | Non-administrator |
| Denied tag | `lab5-restricted` |
| Allowed tags | Unchanged |
| Restricted book | `Lab5-Test-Book-02` |
| Restricted book ID | `3` |

### Step 64C: Define the Expected Access Results

Once the denied-tag restriction is saved, the expected behavior is:

| Book | Administrator | lab5-user-a |
|---|---|---|
| Lab5-Test-Book-01 | Visible | Visible |
| Lab5-Test-Book-02 (ID 3) | Visible | Hidden |

This configuration is intended to create a controlled authorization boundary between the administrator and the low-privilege user.

### Step 64D: Prepare for Authorization Verification

After saving the restriction, I will sign in as `lab5-user-a` and verify whether Book ID 3 is excluded from the account's normal library view.

I will not submit a direct read-status request until the intended restriction is established and documented.

The endpoint under investigation is:

```text
/ajax/toggleread/3
```

A hidden book does not necessarily prove that server-side authorization is enforced. That distinction is what Candidate #1 will investigate.

### Current Lab 5 Status

| Component | Status |
|---|---|
| Test books | 2 |
| Restricted book ID | 3 |
| Restriction tag on Book 2 | Confirmed |
| User account | `lab5-user-a` |
| Denied-tag configuration | In progress |
| Restriction saved | Awaiting confirmation |
| Low-privilege visibility test | Pending |
| Candidate #1 | Not yet tested |

### Next Step

I will add `lab5-restricted` to the **Denied Tags** field and confirm that it appears in the correct section before saving.

**Outcome:** I located the application's user-specific tag restriction controls and began configuring the denied-tag rule for the controlled authorization test.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f5116f83b0700dfa7a5bf8e4f5ad2cac638bd2c9/Screenshot%202026-09-19%20133735.png)


### Step 65: Apply the Denied Tag View Restriction

To establish the authorization boundary for Candidate #1, I continued configuring the permissions for `lab5-user-a`.

**Navigation:**

Admin Settings → Users → Edit Users → lab5-user-a → Allowed/Denied Tags

I selected **Deny** for the following tag:

```text
lab5-restricted
```

This assigns the tag to the user's **View Restriction** configuration.

The intended result is:

- **Lab5-Test-Book-01:** Remains visible to `lab5-user-a`.
- **Lab5-Test-Book-02 (Book ID 3):** Becomes hidden from `lab5-user-a` because it contains the denied tag.
- **Administrator:** Retains access to both books.

### Why This Matters

This restriction establishes the intended access boundary for testing whether Calibre-Web NextGen enforces object-level authorization.

The endpoint under investigation is:

```text
/ajax/toggleread/3
```

Before submitting a direct request, I must confirm that the restriction is saved and that Book ID 3 is unavailable through the low-privilege user's normal interface.

### Next Step

Save the user's restriction settings and verify that `lab5-restricted` appears under **Denied Tags / View Restriction**.

**Result:** The denied-tag restriction has been selected. Saving and verifying its effect remain pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f366986c0c6ea53cf9f713e2e6405c64f39aefb8/Screenshot%202026-09-19%20133807.png)


### Step 66: Save the User's View Restriction

After assigning `lab5-restricted` to the **Denied Tags / View Restriction** setting, I proceeded to save the configuration for `lab5-user-a`.

**Steps:**

1. Click **CLOSE** to exit the Allowed/Denied Tags window.
2. Return to the **Edit User** page for `lab5-user-a`.
3. Scroll to the bottom of the page.
4. Click **SAVE** to apply the user's updated settings.

### Purpose

This configuration is intended to prevent `lab5-user-a` from viewing books assigned the `lab5-restricted` tag.

The expected access conditions are:

- **Book 1:** Visible to `lab5-user-a`.
- **Book 2 (ID 3):** Hidden from `lab5-user-a`.
- **Administrator:** Can access both books.

### Next Step

After saving, I will sign in as `lab5-user-a` and verify that the restriction works as intended.

This will establish the visibility baseline needed before testing the read-status endpoint:

```text
/ajax/toggleread/3
```

**Result:** The denied-tag restriction is configured. Successful saving and enforcement remain to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ec5e4439b1c19175106634a62a5d48bf16818451/Screenshot%202026-09-19%20134043.png)


### Step 67: Test the Low-Privilege User Account

After saving the `lab5-restricted` denied-tag rule for `lab5-user-a`, I proceeded to verify the restriction using a normal user session.

### Step 67A: Log Out of the Administrator Account

I returned to Calibre-Web NextGen at:

```text
http://localhost:8083
```

I logged out of the administrator account to ensure the next test would not use administrative privileges.

### Step 67B: Log In as the Standard User

I prepared to authenticate using the dedicated Lab 5 account:

```text
Username: lab5-user-a
Password: [Lab-only account password]
```

This account was configured with **Allow eBook Viewer** enabled and administrative privileges disabled.

### Step 67C: Verify Normal Book Visibility

After logging in, I will inspect the Books page without modifying any book records.

**Expected results:**

| Book | Expected Visibility |
|---|---|
| Lab5-Test-Book-01 | Visible |
| Lab5-Test-Book-02 (ID 3) | Hidden |

If the restriction works as intended, the low-privilege account should see only **Books (1)**, while the administrator's library contains two books.

### Why This Test Matters

This step establishes the normal user-interface behavior before testing Candidate #1.

The endpoint under investigation is:

```text
/ajax/toggleread/3
```

I need to confirm that Book ID 3 is restricted from `lab5-user-a` before examining whether the server enforces that restriction on direct read-status requests.

### Next Step

I will inspect the Books page while authenticated as `lab5-user-a` and capture a screenshot showing the visible books.

**Result:** The user-specific restriction has been saved. Its behavior under a low-privilege session remains to be verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2c7399779be115b717cf9e85a2c78adb596d163c/Screenshot%202026-09-19%20134359.png)


### Step 68: Verify Normal Book Visibility for the Low-Privilege User

After saving the denied-tag restriction, I logged out of the administrator account and prepared to verify normal application behavior using `lab5-user-a`.

### Step 68A: Open the Books Page

While authenticated as `lab5-user-a`, I selected **Books** from the Calibre-Web NextGen interface.

The purpose was to determine whether the application correctly applied the user's configured view restriction.

### Step 68B: Verify the Expected Visibility Boundary

The administrator's library contains two controlled test books. However, `lab5-user-a` has been configured to deny books tagged `lab5-restricted`.

**Expected results:**

| Book | Expected Result |
|---|---|
| Lab5-Test-Book-01 | Visible |
| Lab5-Test-Book-02 (ID 3) | Hidden |
| Total visible books | Books (1) |

At this stage, I will only inspect the normal Books page.

I will not attempt to access Book ID 3 directly or submit any modified requests.

### Step 68C: Establish the Authorization Control

If the Books page displays only `Lab5-Test-Book-01`, this will confirm that the application applies the configured restriction to the low-privilege user's normal library view.

This establishes the **visibility control condition** for Candidate #1.

It does not yet prove that the server enforces the same restriction when a user submits a direct request to:

```text
/ajax/toggleread/3
```

That distinction will be investigated during the subsequent authorization test.

### Evidence to Capture

I will save a screenshot showing:

- The authenticated `lab5-user-a` session.
- The Books page.
- `Lab5-Test-Book-01` visible.
- `Lab5-Test-Book-02` absent.
- The displayed book count.

### Next Step

I will compare the observed Books page against the expected visibility restriction and document the result.

**Result:** Normal user visibility verification is in progress. The authorization boundary will be considered established at the interface level only after the expected result is confirmed.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2629f36d3d5fb8c3c313dd677616379e657bcc52/Screenshot_19-9-2026_13467_localhost%20second%20book%20invisable.jpeg)


### Step 69: Establish Authorized Book Access Before Read-Status Testing

After signing in as `lab5-user-a`, I confirmed that the configured deny-tag restriction was working in the normal library interface.

**Observed results:**

- The account displayed **Books (1)**.
- `Lab5-Test-Book-01` was visible.
- `Lab5-Test-Book-02` (Book ID 3) was hidden.
- The restriction tag `lab5-restricted` successfully excluded Book 2 from the user's library view.

This established the visibility boundary needed for Candidate #1 — Read-Status Object Authorization.

### Step 69A: Open the Authorized Book

While still authenticated as `lab5-user-a`, I selected:

**Lab5-Test-Book-01**

I did not change its read status or modify its metadata.

### Step 69B: Identify the Book Object ID

My next objective is to identify the application-assigned ID for Book 1.

I will inspect the book details page and browser URL to record the identifier.

**Book ID:** Pending confirmation.

This identifier will be used to establish normal read-status behavior on an object the user is authorized to access.

### Step 69C: Define the Control Test

Before investigating restricted Book ID 3, I need to understand how the application handles a legitimate read-status operation.

The control test will establish:

1. Whether `lab5-user-a` can open Book 1.
2. Which interface control changes a book's read status.
3. Which request the application generates.
4. How the server responds to an authorized request.
5. Whether the read-status change persists.

I will first observe the book details without changing its state.

### Why This Matters

Testing a known authorized object provides a baseline for comparing subsequent requests involving restricted objects.

The endpoint under investigation is:

```text
/ajax/toggleread/<book_id>
```

The fact that Book ID 3 is hidden confirms a user-interface visibility restriction. It does not yet establish whether the server enforces the restriction on direct read-status requests.

### Next Step

I will open `Lab5-Test-Book-01` and record its book ID before performing any read-status action.

**Result:** The low-privilege visibility restriction was confirmed. The authorized-book control test is ready to begin.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/762bb74c4fcf96c3ecb409b924e407fd075faa3d/Screenshot%202026-09-21%20122158.png)

Ctrl + Shift + I.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2a3d7b0c76bde798683302a8a48f7c3e5c96878b/Screenshot%202026-09-21%20125540.png))

At the top of DevTools, click the + next to Console.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ec5a64d2ab1fc05b110a7f3db7ec7005c8b18a58/Screenshot%202026-09-21%20122748.png)

A menu of tools will appear. Select Network.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/89701581acc649431d2993d3868691c281aa30ba/Screenshot%202026-09-21%20122831.png)


### Step 70: Capture the Authorized Read-Status Control Request

With `lab5-user-a` authenticated and `Lab5-Test-Book-01` open, I prepared to capture the application's normal read-status request using the browser's Developer Tools.

This is the first active control test for **Candidate #1 — Read-Status Object Authorization**.

### Step 70A: Prepare the Network Capture

I opened **Developer Tools → Network** and confirmed that network recording was enabled.

To isolate the request generated by the read-status action, I:

1. Clicked **Clear network log (⊘)**.
2. Kept network recording enabled.
3. Remained on the Book 1 details page.

Clearing the log removes earlier browser requests from the visible capture without changing application data.

### Step 70B: Trigger the Authorized Read-Status Action

While authenticated as `lab5-user-a`, I selected the **Mark As Read** icon located beneath the **Read now** button.

I then stopped interacting with the application so I could inspect the resulting network activity.

### Step 70C: Identify the Request

Based on my earlier static analysis, I expected the read-status operation to involve an endpoint matching:

```text
/ajax/toggleread/<book_id>
```

The Network panel may display the request under a numeric name corresponding to the book ID rather than showing the full endpoint.

I will inspect the captured request to identify:

- Request URL and HTTP method
- Book object ID
- HTTP response status
- Request and response data
- Whether the interface reflects the read-status change

I will redact session cookies or authentication tokens before publishing any network evidence.

### Step 70D: Preserve the Control Evidence

This request is important because Book 1 is visible and accessible to the low-privilege account.

A successful authorized request will establish expected behavior for comparison with a later request involving restricted Book ID 3.

I will not modify or submit a request for Book ID 3 during this step.

### Next Step

I will select the newly captured read-status request in the Network panel and examine its headers and response.

**Result:** The authorized control-request capture procedure is established. The actual request and server response remain to be verified.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/49c8b961284896c5304fdfb9fc478821c9aa1147/screenshot.png)


### Step 71: Verify the Authorized Read-Status Request

After clicking **Mark As Read** on `Lab5-Test-Book-01`, I captured the resulting request in Chrome Developer Tools.

This provides the initial control evidence for **Candidate #1 — Read-Status Object Authorization**.

### Step 71A: Review the Network Capture

The Network panel displayed the following results:

| Field | Observed Value |
|---|---|
| Request Name | `2` |
| Status | `200` |
| Type | `xhr` |
| Requests Captured | 1 |
| Authenticated User | `lab5-user-a` |
| Target Book | `Lab5-Test-Book-01` |
| Book ID | `2` |

The HTTP 200 response indicates that the server processed the request successfully at the HTTP level.

### Step 71B: Inspect the Request Headers

To verify the exact endpoint and HTTP method, I selected the request named `2` in the Network panel.

I then opened the **Headers** tab and located the **General** section.

**Expected request details:**

```http
Request URL: http://localhost:8083/ajax/toggleread/2
Request Method: POST
Status Code: 200 OK
```

These values are based on the endpoint identified during static analysis and the observed Network request.

The exact URL and method still need confirmation from the Headers panel.

### Step 71C: Explain the Control Test

Book ID 2 is visible and accessible to `lab5-user-a`.

Therefore, this request establishes the expected behavior of the read-status endpoint when the user interacts with an authorized book.

The next phase will compare this behavior against Book ID 3, which is hidden by the user's `lab5-restricted` deny-tag rule.

**Authorization comparison:**

- Book ID 2 — Authorized control object.
- Book ID 3 — Restricted test object.

A successful response for Book ID 2 does not establish whether the endpoint correctly enforces authorization for Book ID 3.

### Step 71D: Preserve the Evidence

I will capture the Network Headers panel showing the request URL, HTTP method, and response status.

Before publishing the screenshot, I will ensure that session cookies and authentication tokens are not exposed.

### Next Step

I will verify the request details in the Headers panel before modifying or replaying any requests.

**Result:** The authorized read-status action generated one XHR request named `2`, which returned HTTP 200. Verification of the exact request URL and method remains pending.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6d555c9f4a7382ff04b33f1b1e01917e9315190e/Screenshot%202026-09-26%20213259.png)


### Step 72: Prepare the Manipulated Read-Status Authorization Request

After verifying the legitimate read-status request for Book ID 2, I proceeded to the primary dynamic test for **Candidate #1 — Read-Status Object Authorization**.

The objective is to determine whether Calibre-Web NextGen enforces the configured book visibility restriction when a low-privilege user submits a request containing a restricted book ID.

### Step 72A: Confirm the Authorized Control Request

Using Chrome Developer Tools, I previously captured and verified:

```http
Request URL: http://localhost:8083/ajax/toggleread/2
Request Method: POST
Status Code: 200 OK
```

This request was generated while authenticated as `lab5-user-a`, using a book the account was authorized to access.

### Step 72B: Prepare the Restricted-Object Test

The test environment contains two book objects:

| Book | ID | Access for lab5-user-a |
|---|---|---|
| Lab5-Test-Book-01 | 2 | Visible |
| Lab5-Test-Book-02 | 3 | Hidden by denied tag |

The restricted book has the tag:

```text
lab5-restricted
```

I confirmed earlier that `lab5-user-a` sees only **Books (1)** in the normal library interface.

### Step 72C: Edit the Captured Request

While authenticated as `lab5-user-a`, I returned to:

```text
http://localhost:8083/book/2
```

I kept Developer Tools open and located the previously captured read-status request.

**Procedure:**

1. Open the **Network** panel.
2. Right-click the request named `2`.
3. Select **Edit and Resend**.
4. Locate the **Request URL** field.
5. Change only the final book ID from `2` to `3`.

**Original authorized request:**

```http
POST http://localhost:8083/ajax/toggleread/2
```

**Modified request:**

```http
POST http://localhost:8083/ajax/toggleread/3
```

I will preserve the original HTTP method, request body, and applicable authenticated-session context.

At this stage, the modified request has not yet been submitted.

### Step 72D: Define the Expected Security Behavior

The purpose of this test is to determine whether the server checks the requesting user's authorization for the specified book object.

If the denied-tag restriction is intended to prohibit read-status modifications, the expected secure behavior is for the server to reject the unauthorized operation without changing Book ID 3's state.

A successful HTTP response alone would not be sufficient to confirm a vulnerability. I would also need to verify the application's authorization rules and whether the restricted book's read-status data actually changed.

### Next Step

I will verify that the edited request targets Book ID 3 and that no other request parameters were changed.

I will then submit the request once and inspect the response.

**Result:** The legitimate control request was verified as HTTP POST with status 200. The restricted-object request has been prepared for controlled authorization testing, but its result is not yet known.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b7d4f005007c6e02426f92f2cff0147811dd1bfd/Screenshot%202026-09-26%20220251.png)


### Step 73: Execute the Manipulated Read-Status Request

After verifying the legitimate control request for Book ID 2, I prepared the modified request for **Candidate #1 — Read-Status Object Authorization**.

### Step 73A: Confirm the Request Modification

Using Chrome Developer Tools → Network → Edit and Resend, I confirmed:

- **Authenticated account:** `lab5-user-a`
- **HTTP method:** `POST`
- **Original endpoint:** `/ajax/toggleread/2`
- **Modified endpoint:** `/ajax/toggleread/3`
- **Modified variable:** Book ID changed from `2` to `3`
- **Request execution:** Not yet performed

**Modified request:**

```http
POST http://localhost:8083/ajax/toggleread/3
```

Book ID 3 belongs to `Lab5-Test-Book-02`, which is hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.

### Step 73B: Execute the Controlled Request

With the modified URL verified, the next action is to click the blue **Send** button in Developer Tools.

I will submit the request only once and observe the server response.

### Step 73C: Collect the Response Evidence

After sending the request, I will record:

1. HTTP response status code.
2. Response body.
3. Whether the server returns an authorization error.
4. Whether the request produces an application-level success message.

I will preserve the request and response evidence without exposing authentication cookies or session tokens.

### Step 73D: Evaluate the Authorization Boundary

The test compares two requests made under the same low-privilege session:

| Test | Book ID | Expected Authorization |
|---|---|---|
| Authorized control | 2 | Allowed |
| Restricted-object test | 3 | Denied if the visibility rule also prohibits modification |

A `200 OK` response to the restricted-object request would warrant further investigation, but would not alone prove an authorization vulnerability.

I must determine whether the request actually changed Book ID 3's read status and whether the application's intended access rules prohibit that modification.

### Next Step

Submit the modified request and inspect the response in Developer Tools.

**Result:** The modified request is ready for execution. No conclusion about the server's authorization enforcement can be made until the response and any resulting state change are verified.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/e888346cb4aa397635d7a6e39ec477acfad4dcfe/Screenshot%202026-09-26%20220817.png)


### Step 74: Investigate the FETCH_ERROR and Verify Server-Side Request Delivery

During Candidate #1 — Read-Status Object Authorization, I attempted to submit a modified request targeting restricted Book ID 3.

**Manipulated request:**

```http
POST http://localhost:8083/ajax/toggleread/3
```

The request was prepared using Microsoft Edge Developer Tools → Network → Edit and Resend.

### Step 74A: Document the Observed Error

After clicking Send, Edge displayed:

```text
Status: FETCH_ERROR
Size: 0 bytes
```

This result did not provide an HTTP response from Calibre-Web NextGen.

Therefore, I could not determine whether the application accepted or rejected the modified request.

**Important:** `FETCH_ERROR` is a browser tooling result, not evidence of an HTTP 403, HTTP 200, or successful authorization bypass.

### Step 74B: Inspect the Container Logs

To investigate whether the request reached the application, I moved to PowerShell.

**Command:**

```powershell
docker logs calibre-web-nextgen --tail 30
```

This command retrieves the latest 30 log lines from the running Calibre-Web NextGen container.

I will look for an entry associated with:

```text
POST /ajax/toggleread/3
```

### Step 74C: Interpret the Results

**If the request appears in the logs:**

I will record any available timestamp, HTTP status, and application messages. I will then independently verify whether Book ID 3's read-status data changed.

**If the request does not appear:**

I will investigate an alternative controlled method of submitting the authenticated request without relying on Edge's Edit and Resend feature.

The absence of a matching entry in the last 30 log lines would not conclusively prove the request never reached the server, because request logging may be incomplete or the relevant entry may be outside the displayed range.

### Step 74D: Preserve the Authorization Test Evidence

The established control conditions remain:

- `lab5-user-a` can access Book ID 2.
- Book ID 3 is hidden by the `lab5-restricted` deny-tag rule.
- The authorized `POST /ajax/toggleread/2` request returned HTTP 200.
- The modified request targeting Book ID 3 produced `FETCH_ERROR` in Edge.
- No server-side authorization result has been confirmed.

### Next Step

I will inspect the container logs to determine whether there is evidence that the modified request reached Calibre-Web NextGen.

**Outcome:** The first manipulated-request attempt was inconclusive because Edge did not return an HTTP response. Candidate #1 remains unconfirmed pending server-side evidence and state verification.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7791711e8089e56e036f454d3820784fcf4795a2/Screenshot%202026-09-26%20222554.png)


### Step 75: Prepare a Browser Console Request to Verify Object-Level Authorization

After the manipulated request returned `FETCH_ERROR` in Microsoft Edge, I inspected the Calibre-Web NextGen container logs.

The visible log entries showed read-status activity for Book ID 2, including:

```text
Book 2 readbit toggled
```

However, I found no corresponding entry showing:

```text
Book 3 readbit toggled
```

This suggests that the modified request may not have reached the application successfully. The absence of a Book 3 log entry does not conclusively establish that the request was never received.

**Candidate #1 remains inconclusive.**

### Step 75A: Use the Authenticated Browser Console

To avoid the issue encountered with Edge's Edit and Resend feature, I prepared to use the browser's JavaScript Console.

While authenticated as `lab5-user-a`, I:

1. Returned to the authorized Book 1 page at `http://localhost:8083/book/2`.
2. Opened Microsoft Edge Developer Tools.
3. Selected the **Console** tab.
4. Kept the existing authenticated browser session active.

### Step 75B: Define the Controlled Request

The previously verified authorized request was:

```http
POST http://localhost:8083/ajax/toggleread/2
```

The restricted-object test will target:

```http
POST http://localhost:8083/ajax/toggleread/3
```

The objective is to submit one request using the same authenticated session and compare the server's response against the authorized control.

Before execution, I will preserve the original request method and any required request parameters or security headers.

### Step 75C: Define the Evidence Requirements

For the Console-based test, I will collect:

- Actual HTTP response status
- Response body
- Relevant server-side log entries
- Evidence of whether Book ID 3's read status changed

A successful HTTP response alone will not establish an authorization vulnerability. I must also verify the resulting state and the application's intended access restrictions.

### Next Step

I will use the Console to submit one controlled request targeting Book ID 3, then inspect the response and application logs.

**Outcome:** The Edge Edit and Resend attempt remained inconclusive. I selected the authenticated browser Console as an alternative testing method to obtain a verifiable server response.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7529fe664d75940fb869236ae68badd4b8bb550a/Screenshot%202026-09-26%20221251.png)


### Step 76: Prepare the Authenticated Console Request and Address Edge Paste Protection

After the unsuccessful Edit and Resend attempt, I switched to Microsoft Edge's JavaScript Console to continue **Candidate #1 — Read-Status Object Authorization**.

The purpose was to submit a controlled request using the existing authenticated session for `lab5-user-a`.

### Step 76A: Prepare the Request

While logged in as `lab5-user-a`, I opened:

**Developer Tools → Console**

I prepared the following JavaScript command:

```javascript
fetch('/ajax/toggleread/3', {method:'POST'})
  .then(async r => console.log('STATUS:', r.status, 'BODY:', await r.text()))
```

This command:

- Sends an HTTP `POST` request to Book ID 3.
- Uses the browser's existing same-origin authenticated session.
- Displays the actual HTTP response status.
- Displays the response body.

The endpoint targets `Lab5-Test-Book-02`, which is hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.

### Step 76B: Identify Edge's Self-XSS Protection

When I attempted to paste the command into the Console, Microsoft Edge displayed a yellow security warning.

This is a browser protection designed to discourage users from executing potentially harmful JavaScript copied from untrusted sources.

The warning prevented the command from being pasted immediately.

Edge provided an option to enable pasting by manually entering:

```text
allow pasting
```

I documented this browser security behavior before continuing.

Because the command is part of my authorized local testing procedure, I can review it before choosing whether to enable Console pasting.

### Step 76C: Define the Expected Evidence

After executing the request, I will look for output similar to:

```text
STATUS: [HTTP status code]
BODY: [server response]
```

These values have not yet been observed.

The response will help determine how the application handles a read-status request for a book hidden from the current user.

### Step 76D: Preserve the Investigation Scope

The test will use:

- The existing `lab5-user-a` session.
- The local application at `localhost:8083`.
- HTTP method `POST`.
- Restricted Book ID `3`.
- One controlled request.

I will not interpret a successful HTTP response as proof of a vulnerability without independently checking the resulting book state and confirming the intended authorization rules.

### Next Step

I will address Edge's Console paste protection, execute the reviewed request once, and record the actual response.

**Outcome:** I prepared an authenticated browser-based request to replace the inconclusive Edit and Resend attempt. Edge's self-XSS paste protection was encountered, and the request has not yet been executed.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0b93e8a6b0395d17956ceca92dfa686a09bce5c8/Screenshot%202026-09-26%20223821.png)


### Step 77: Manually Execute the Authenticated Read-Status Request

During Candidate #1 — Read-Status Object Authorization, Microsoft Edge prevented me from pasting the test command into the Developer Tools Console.

I attempted to enter:

```text
allow pasting
```

However, Edge interpreted the text as JavaScript and returned:

```text
Unexpected identifier 'pasting'
```

This was a browser Console error. **No request was sent to Calibre-Web NextGen during this attempt.**

### Step 77A: Use Manual Console Entry

To continue without disabling browser security protections, I chose to manually type the request.

While authenticated as `lab5-user-a`, I opened **Developer Tools → Console** and prepared the following command:

```javascript
fetch('/ajax/toggleread/3',{method:'POST'})
```

This command uses the current authenticated browser session to send a POST request to the read-status endpoint for restricted Book ID 3.

### Step 77B: Maintain the Authorization Test Conditions

The test compares:

- **Book ID 2:** Visible to `lab5-user-a`; authorized control request returned HTTP 200.
- **Book ID 3:** Hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.
- **HTTP method:** POST.
- **Application:** Local Calibre-Web NextGen instance at `localhost:8083`.

Only the target book ID differs from the previously verified control request.

### Step 77C: Understand the Console Response

The `fetch()` command returns a JavaScript Promise.

A result such as:

```text
Promise {<pending>}
```

does not establish whether the server accepted or rejected the request.

I will need to inspect the resolved HTTP response status and body before interpreting the result.

### Next Step

I will manually type the `fetch()` command, execute it once, and inspect the response.

Afterward, I will independently verify the server logs and Book ID 3's read-status state before determining whether an authorization vulnerability exists.

**Outcome:** The Console paste attempt failed without sending a request. Manual entry was selected as the next controlled testing method. Candidate #1 remains unconfirmed.

![image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/883a7134efb229f026ece08aa42681fcc8072f97/Screenshot%202026-09-26%20224343.png)


### Step 78: Investigate the HTTP 400 Response for Restricted Book ID 3

During Candidate #1 — Read-Status Object Authorization, I successfully submitted the manipulated request using Microsoft Edge's JavaScript Console while authenticated as `lab5-user-a`.

Unlike the earlier Edit and Resend attempt, this request reached the application and produced an HTTP response.

### Step 78A: Document the Control and Manipulated Requests

**Authorized control request:**

```http
POST http://localhost:8083/ajax/toggleread/2
```

**Observed response:** `200 OK`

Book ID 2 is visible to `lab5-user-a`, making it the authorized control object.

**Manipulated request:**

```http
POST http://localhost:8083/ajax/toggleread/3
```

**Observed response:** `400 Bad Request`

Book ID 3 belongs to `Lab5-Test-Book-02`, which is hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.

### Step 78B: Interpret the Initial Response

The HTTP 400 response confirms that the manipulated request did not receive the same successful response as the authorized control.

However, this result does not establish why the server rejected the request.

Possible explanations include:

- Object-level authorization enforcement.
- Missing or invalid request parameters.
- CSRF or other request-validation requirements.
- Application-specific error handling.

The Console request was not necessarily identical to the original browser-generated request apart from the book ID, so these possibilities must be investigated before drawing a conclusion.

### Step 78C: Inspect Server-Side Logs

To investigate the application's handling of the request, I returned to PowerShell.

**Command:**

```powershell
docker logs calibre-web-nextgen --tail 20
```

This command retrieves the latest 20 log lines from the Calibre-Web NextGen container.

I will inspect entries corresponding to the time of the manipulated request, particularly references to:

```text
/ajax/toggleread/3
Book 3
readbit
400
```

### Step 78D: Verify Whether the Restricted Object Changed

The HTTP response alone cannot establish whether Book ID 3's read-status state was modified.

After reviewing the logs, I will independently inspect the relevant server-side state to determine whether the request caused a change.

This is important because an application may return an error even when an underlying operation has partially executed.

### Next Step

I will review the container logs, identify any messages associated with the HTTP 400 response, and determine what additional evidence is needed.

**Outcome:** The manipulated request reached Calibre-Web NextGen and returned HTTP 400, compared with HTTP 200 for the authorized control. The reason for the response and the effect on Book ID 3 remain unverified. Candidate #1 is still inconclusive.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ee05ef5bfa0ad9bc29a6cc53488429e1d1572374/Screenshot%202026-09-26%20224655.png)


### Step 79: Identify the Missing CSRF Token and Inspect the Legitimate Request

During Candidate #1 — Read-Status Object Authorization, I reviewed the Calibre-Web NextGen container logs to determine why the manipulated request returned HTTP 400.

The logs revealed the following message:

```text
INFO {flask_wtf.csrf:263} The CSRF token is missing.
```

This established that the request failed CSRF validation before it could provide a meaningful object-level authorization result.

### Step 79A: Identify the Cause of the HTTP 400 Response

The manipulated request was submitted through Microsoft Edge's JavaScript Console:

```javascript
fetch('/ajax/toggleread/3',{method:'POST'})
```

The request reached the application but returned:

```http
400 Bad Request
```

The server log identified the missing CSRF token as the cause of this response.

**Finding:** The HTTP 400 response cannot be treated as evidence that object-level authorization successfully blocked access to Book ID 3.

Candidate #1 remains unresolved.

### Step 79B: Return to the Authorized Control Request

To prepare a valid comparison, I returned to Microsoft Edge Developer Tools.

I navigated to:

**DevTools → Network → XHR request `2` → Payload**

The authorized control request was:

```http
POST http://localhost:8083/ajax/toggleread/2
```

This request previously returned HTTP 200 while authenticated as `lab5-user-a`.

### Step 79C: Inspect the Original Request Data

I will inspect the Payload tab to identify the request data used by the legitimate application interface.

The investigation will focus on:

- CSRF token field and value, if present.
- Form data or request body.
- Request content type.
- Any additional parameters required by the endpoint.

I will also review the request headers if the CSRF token is not present in the Payload tab.

Any live CSRF token or session credential will be excluded from public GitHub documentation.

### Step 79D: Prepare an Equivalent Authorization Test

The objective is to reproduce the legitimate request while changing only the target book ID.

**Authorized control:**

```http
POST /ajax/toggleread/2
```

**Restricted-object test:**

```http
POST /ajax/toggleread/3
```

Both requests must preserve the same authenticated user context and required request data.

This will allow me to distinguish request-validation failures from the application's object-level authorization behavior.

### Next Step

I will inspect the legitimate request's Payload tab and document the required request structure before conducting another controlled test.

**Outcome:** Server logs confirmed that the previous HTTP 400 response resulted from a missing CSRF token. The investigation now requires a valid authenticated request containing the same CSRF data as the authorized control.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/457fa3c85a237dad6676bdf3866438d9379eb628/Screenshot%202026-09-26%20225326.png)


### Step 80: Prepare the CSRF-Preserving Authorization Test

During Candidate #1 — Read-Status Object Authorization, I confirmed that the legitimate read-status request contains form data with a `csrf_token`.

This explains why the earlier JavaScript Console request returned HTTP 400 with the server log message:

```text
The CSRF token is missing.
```

The next test will preserve the legitimate request structure while changing only the target book ID.

#### Step 80A: Reopen the Legitimate Request

While authenticated as `lab5-user-a`, I returned to:

**Microsoft Edge → Developer Tools → Network**

I located the legitimate XHR request named `2`:

```http
POST http://localhost:8083/ajax/toggleread/2
```

This request previously returned `200 OK`.

I right-clicked the request and selected **Edit and Resend**.

#### Step 80B: Prepare the Manipulated Request

The legitimate request contains form data including:

```text
csrf_token=[REDACTED]
```

For the controlled authorization test, I will preserve:

- HTTP method: `POST`
- Authenticated session cookie
- CSRF token and form data
- Content-Type header
- Other legitimate request headers and parameters

I will change only the URL.

**Original URL:**

```text
http://localhost:8083/ajax/toggleread/2
```

**Modified URL:**

```text
http://localhost:8083/ajax/toggleread/3
```

Book ID 3 is hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.

#### Step 80C: Verify the Request Before Sending

Before clicking Send, I will confirm that:

1. The HTTP method remains `POST`.
2. The URL targets Book ID 3.
3. The original request body still contains `csrf_token`.
4. The session and required headers remain unchanged.
5. No other request parameters were modified.

I will not expose the CSRF token or session cookie in my public GitHub documentation.

#### Step 80D: Define the Expected Evidence

Once the request is submitted, I will record the actual HTTP status, response body, relevant application logs, and any resulting change to Book ID 3's read-status state.

A successful response alone will not prove an authorization vulnerability. The server-side state must be independently verified.

### Next Step

I will inspect the edited request before submitting it.

**Outcome:** The legitimate request's CSRF form data has been identified. A CSRF-preserving manipulated request is being prepared, but it has not yet been sent. Candidate #1 remains unresolved.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f3c89eca377a23c454b2ed35d3f694106f02b2e7/Screenshot%202026-09-26%20231639.png)


### Step 81: Locate the CSRF Token in the Authenticated Page DOM

During Candidate #1 — Read-Status Object Authorization, I confirmed that the legitimate read-status request included a `csrf_token` form field.

To avoid manually copying a sensitive token, I prepared to check whether the authenticated page exposes it through the Document Object Model (DOM).

#### Step 81A: Inspect the Page DOM

While logged in as `lab5-user-a`, I opened Microsoft Edge Developer Tools and selected the **Console** tab.

I manually entered:

```javascript
document.querySelector('[name="csrf_token"]')?.value
```

This command searches the current page for the first element whose `name` attribute is `csrf_token` and retrieves its value, if the element exists.

#### Step 81B: Interpret the Result

Possible results include:

- **A token value:** A matching element exists and exposes a value.
- **`undefined`:** No matching element was found, or the selected element has no accessible `value` property.
- **An empty string:** A matching element exists, but its value is empty.

A token found in the DOM is not automatically proof that it matches the token used by the original read-status request.

#### Step 81C: Protect Sensitive Data

The CSRF token is security-sensitive session data.

I will not publish its value in the GitHub README, screenshots, or shared Console output.

#### Next Step

I will execute the DOM query and determine whether a CSRF token is accessible before preparing another controlled request.

**Outcome:** DOM inspection has been selected as the next step. No new manipulated request has been submitted, and Candidate #1 remains unresolved.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d96128923ddf749e1896b064a71a82cd1d46e22f/Screenshot%202026-09-26%20231639.png)


### Step 82: Investigate the Console DOM Error and Recover the Original CSRF Form Data

During Candidate #1 — Read-Status Object Authorization, I attempted to retrieve the CSRF token from the authenticated page using Microsoft Edge Developer Tools.

#### Step 82A: Execute the DOM Query

While authenticated as `lab5-user-a`, I entered:

```javascript
document.querySelector('[name="csrf_token"]')?.value
```

Microsoft Edge returned:

```text
Uncaught TypeError: document.querySelector is not a function
```

This indicates that the `document` reference in the current Console execution context did not expose the expected `querySelector()` function.

The cause has not yet been established.

**No HTTP request was submitted, and no server-side state change was observed from this command.**

#### Step 82B: Return to the Legitimate Network Request

Rather than continuing to troubleshoot the DOM query, I decided to retrieve the CSRF form data from the previously captured successful request.

I navigated to:

**Microsoft Edge → Developer Tools → Network → Request `2` → Payload**

The legitimate control request was:

```http
POST http://localhost:8083/ajax/toggleread/2
```

**Previously observed response:** `200 OK`

The Payload tab showed form data containing a `csrf_token` field.

#### Step 82C: Inspect the URL-Encoded Payload

Within the Payload tab, I selected **View source** next to Form Data.

This view is intended to reveal the original URL-encoded request body, including the CSRF field.

Expected structure:

```text
csrf_token=[REDACTED]
```

The exact payload has not yet been recorded.

I will preserve the original field names, encoding, and any additional parameters when preparing the next request.

The live CSRF token will not be included in my public GitHub documentation.

#### Step 82D: Prepare a Controlled Authorization Comparison

The objective remains to compare two authenticated requests:

**Authorized control:**

```http
POST /ajax/toggleread/2
```

**Restricted-object test:**

```http
POST /ajax/toggleread/3
```

The next test must preserve the legitimate request body, session, CSRF data, and relevant headers while changing only the target book ID.

This will help distinguish CSRF validation failures from object-level authorization behavior.

### Outcome

The DOM-based CSRF lookup failed with a JavaScript TypeError. I returned to the legitimate Network request to inspect its original URL-encoded form data.

**Candidate #1 remains unresolved.** No new authorization result has been obtained.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/becb8688386307fc4a27b9fce31bde21c72bb76e/Screenshot%202026-09-26%20232744.png)


### Step 83: Recover the CSRF Form Field for the Controlled Authorization Test

During Candidate #1 — Read-Status Object Authorization, I inspected the successful request in Microsoft Edge Developer Tools.

Under **Network → Request `2` → Payload → View source**, I confirmed that the legitimate request body contained only one form field:

```text
csrf_token=[REDACTED]
```

#### Step 83A: Confirm the Cause of the Previous HTTP 400

The earlier manipulated request returned HTTP 400, and the application logs reported:

```text
The CSRF token is missing.
```

The original request payload confirmed that the legitimate application sends the CSRF token as URL-encoded form data.

Therefore, the previous HTTP 400 cannot be interpreted as an object-level authorization rejection.

#### Step 83B: Prepare the CSRF Form Data

I identified the token in the legitimate Network request and prepared to store it in the browser Console for reuse.

The intended operation is:

```javascript
x.set('csrf_token','[REDACTED]')
```

This requires `x` to be an existing `URLSearchParams` object.

The actual token is excluded from this public documentation because it is sensitive session-related data.

#### Step 83C: Preserve the Authorization Test Conditions

The next controlled test will preserve the legitimate request's:

- Authenticated `lab5-user-a` session.
- HTTP POST method.
- URL-encoded form body.
- CSRF token.
- Required request headers.

The intended change is limited to the target endpoint:

```text
Authorized:  /ajax/toggleread/2
Restricted:  /ajax/toggleread/3
```

### Outcome

I confirmed that the successful control request contains a single `csrf_token` form field and identified the missing request data responsible for the earlier HTTP 400.

**Candidate #1 remains unresolved.** The CSRF-preserving manipulated request has not yet produced a verified authorization result.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ee6b18404b1ffe4f3b469b5019dec3dbfb46a0e3/Screenshot%202026-09-27%20001246.png)


### Step 84: Correct CSRF Form Encoding and Prepare the Restricted-Book Request

During Candidate #1 — Read-Status Object Authorization, I created a `URLSearchParams` object in Microsoft Edge's Console to reproduce the legitimate request body.

```javascript
var x = new URLSearchParams()
```

I then populated the CSRF field using the token recovered from the legitimate Network request.

However, the entered value included the `csrf_token=` prefix, which could result in incorrect form encoding.

#### Step 84A: Correct the CSRF Field

The expected request body is:

```text
csrf_token=[REDACTED]
```

The `URLSearchParams` object must contain the token value without repeating the field name.

#### Step 84B: Prepare the Manipulated Request

After correcting the CSRF field, I prepared:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',body:x})
```

This request uses the authenticated `lab5-user-a` browser session and targets restricted Book ID 3.

#### Step 84C: Verification Requirements

I will collect the HTTP response status, server logs, and evidence of whether Book ID 3's read-status state changed.

A successful HTTP response alone will not establish an authorization vulnerability.

**Outcome:** The request is prepared but has not yet been executed with verified CSRF encoding. Candidate #1 remains unresolved.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/125f8a9535692410ab07f0744f57ede5b2e5bb8f/Screenshot%202026-09-27%20114846.png)


### Step 84: Correct CSRF Form Encoding and Prepare the Restricted-Book Request

During Candidate #1 — Read-Status Object Authorization, I created a `URLSearchParams` object in Microsoft Edge's Console to reproduce the legitimate request body.

```javascript
var x = new URLSearchParams()
```

I then populated the CSRF field using the token recovered from the legitimate Network request.

However, the entered value included the `csrf_token=` prefix, which could result in incorrect form encoding.

#### Step 84A: Correct the CSRF Field

The expected request body is:

```text
csrf_token=[REDACTED]
```

The `URLSearchParams` object must contain the token value without repeating the field name.

#### Step 84B: Prepare the Manipulated Request

After correcting the CSRF field, I prepared:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',body:x})
```

This request uses the authenticated `lab5-user-a` browser session and targets restricted Book ID 3.

#### Step 84C: Verification Requirements

I will collect the HTTP response status, server logs, and evidence of whether Book ID 3's read-status state changed.

A successful HTTP response alone will not establish an authorization vulnerability.

**Outcome:** The request is prepared but has not yet been executed with verified CSRF encoding. Candidate #1 remains unresolved.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/652890e69a832c42ee310c356bdba34b64ebea4c/Screenshot%202026-09-27%20114338.png)


### Step 85: Refresh the CSRF Token and Reestablish the Authorized Control

During Candidate #1 — Read-Status Object Authorization, the manipulated request encountered another CSRF validation issue.

The current working hypothesis is that the previously captured CSRF token was stale or no longer valid for the active browser session.

Because CSRF validation must succeed before object-level authorization can be meaningfully evaluated, I paused the manipulated-request testing.

#### Step 85A: Refresh the Authenticated Session

While remaining logged in as `lab5-user-a`, I prepared to refresh the Book 1 page:

```text
http://localhost:8083/book/2
```

I will use `Ctrl+F5` to reload the page and obtain its current application state.

#### Step 85B: Capture a Fresh Legitimate Request

In Microsoft Edge Developer Tools, I will:

1. Open **Network → Fetch/XHR**.
2. Clear the existing Network log.
3. Click **Mark As Read** once on Book 1.
4. Locate the new request named `2`.
5. Select **Payload → View source**.
6. Inspect the freshly generated URL-encoded request body.

The expected form structure is:

```text
csrf_token=[REDACTED]
```

I will also record the HTTP response status to confirm that the authorized control request still works.

#### Step 85C: Preserve the Test Evidence

I will document the following information:

- Authenticated account: `lab5-user-a`
- Authorized control object: Book ID 2
- Request method: POST
- Endpoint: `/ajax/toggleread/2`
- CSRF form field: `csrf_token`
- HTTP response status: Pending verification

The actual token value will remain private and will not be included in the GitHub repository.

#### Step 85D: Maintain the Testing Boundary

I will not submit another manipulated request until the refreshed authorized control succeeds and its request structure has been verified.

The next authorization test will target restricted Book ID 3 while preserving the authenticated session and required CSRF data.

### Outcome

I identified the need to refresh the CSRF state and capture a new legitimate request before continuing Candidate #1.

The refreshed control request has not yet been verified, and no object-level authorization vulnerability has been confirmed.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/985b8d123ce9a39c9a34b950ac508e375d852526/Screenshot%202026-09-27%20003528.png)

The hard refresh worked. The token in your screenshot is different from the expired one. The first portion is similar, but the signed/time-dependent portion has changed.


### Step 86: Update the CSRF Token and Execute the Restricted-Book Test

During Candidate #1 — Read-Status Object Authorization, I generated a fresh CSRF token through the legitimate Book ID 2 read-status request.

The next objective is to reuse that token in a controlled request targeting restricted Book ID 3.

#### Step 86A: Update the CSRF Form Data

While authenticated as `lab5-user-a`, I returned to Microsoft Edge Developer Tools → Console.

The existing `URLSearchParams` object, `x`, was retained.

I prepared the following command to replace the previous CSRF token:

```javascript
x.set('csrf_token','[FRESH_TOKEN]')
```

The token value is redacted from this public documentation.

The expected Console result is:

```text
undefined
```

This indicates that the `set()` operation completed without returning a value. It does not independently confirm that the token is valid.

#### Step 86B: Prepare the Manipulated Request

Without refreshing the page or triggering another read-status operation, I prepared:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',body:x})
```

The request is intended to preserve:

- Authenticated user: `lab5-user-a`
- HTTP method: `POST`
- Form data containing the fresh CSRF token
- Same-origin browser session
- Read-status operation

The target changes from authorized Book ID 2 to restricted Book ID 3.

#### Step 86C: Capture the Authorization Evidence

After submitting the request once, I will examine:

1. The HTTP response status.
2. The response body.
3. Application logs, including CSRF validation messages.
4. Whether Book ID 3's read-status state changed.

A JavaScript Promise result alone will not establish the HTTP response or whether the operation succeeded.

#### Step 86D: Determine the Next Investigation Step

If the request passes CSRF validation, I can begin evaluating the application's authorization behavior.

If CSRF validation fails again, I will inspect the request's actual form data and compare it with the legitimate control before making another attempt.

### Outcome

A fresh CSRF token was obtained from the legitimate control request, and the restricted-object test was prepared using the existing authenticated browser session.

**The manipulated request's result is pending. Candidate #1 remains unresolved.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1c315d64ee56159a91ebce6723ca7e88c13798cc/Screenshot%202026-09-27%20004210.png)


### Step 87: Execute the CSRF-Preserving Object-Level Authorization Test

During Candidate #1 — Read-Status Object Authorization, I prepared a manipulated HTTP request using the authenticated `lab5-user-a` browser session.

The `URLSearchParams` object `x` was updated with the fresh CSRF token obtained from the legitimate Book ID 2 request.

#### Step 87A: Submit the Manipulated Request

In Microsoft Edge Developer Tools → Console, I prepared the following command:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',body:x})
```

This request is intended to:

- Use the authenticated `lab5-user-a` session.
- Send an HTTP POST request.
- Include the freshly captured CSRF token as form data.
- Target restricted Book ID 3.
- Perform the same read-status operation as the legitimate control.

#### Step 87B: Compare the Authorization Boundary

**Authorized control:**

```http
POST /ajax/toggleread/2
```

Previously observed response: `200 OK`

**Manipulated request:**

```http
POST /ajax/toggleread/3
```

Response: Pending execution and verification.

Book ID 3 is hidden from `lab5-user-a` through the `lab5-restricted` denied-tag rule.

#### Step 87C: Collect Evidence

After executing the request once, I will inspect the Network tab for the actual HTTP status and response body.

I will then review the application logs and independently verify whether Book ID 3's read-status state changed.

An HTTP 200 response alone will not establish an authorization vulnerability. I must confirm that an unauthorized state-changing operation occurred.

### Outcome

The CSRF-preserving manipulated request is ready for execution.

**Candidate #1 remains unresolved until the HTTP response and server-side state are verified.**


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4703d524da2b289d5d9d2218a4898a7e22efad2b/Screenshot%202026-09-27%20122159.png)


### Step 88: Inspect Server Logs After the Manipulated Authorization Request

During Candidate #1 — Read-Status Object Authorization, I prepared a POST request targeting restricted Book ID 3 using the authenticated `lab5-user-a` session and refreshed CSRF form data.

The next objective is to determine how Calibre-Web NextGen processed the request.

#### Step 88A: Retrieve the Application Logs

In PowerShell, I will execute:

```powershell
docker logs calibre-web-nextgen --tail 5
```

This command retrieves the five most recent log lines from the Calibre-Web NextGen container.

#### Step 88B: Identify the Server Response

I will examine the logs for:

- CSRF validation errors.
- Authentication or authorization failures.
- Read-status activity involving Book ID 3.
- Application exceptions or request-validation errors.

If the logs show another CSRF failure, the request cannot yet be used to evaluate object-level authorization.

If the request passes CSRF validation, I will investigate whether the application permitted or rejected the restricted operation.

#### Step 88C: Verify the Server-Side State

Regardless of the HTTP response, I will independently verify whether Book ID 3's read-status state changed.

This additional verification is necessary before determining whether Candidate #1 represents a confirmed authorization vulnerability.

### Outcome

The next investigation step is to inspect the container logs for evidence explaining the manipulated request's behavior.

**Candidate #1 remains unresolved pending the log results and server-side state verification.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/512dd4cf3586171349f05fb35bd1ef2ebe29b536/Screenshot%202026-09-27%20004826.png)


### Step 89: Inspect the HTTP Response for the Restricted Book Request

During Candidate #1 — Read-Status Object Authorization, I reviewed the latest Calibre-Web NextGen container logs after submitting the manipulated request targeting Book ID 3.

#### Step 89A: Review the Application Logs

The latest visible read-status events were:

```text
04:32:20 Book 2 readbit toggled
04:32:24 Book 2 readbit toggled
```

Both events reference Book ID 2, the authorized control object.

No corresponding `Book 3 readbit toggled` event appeared in the five retrieved log lines.

However, Microsoft Edge reported:

```http
POST /ajax/toggleread/3
400 Bad Request
```

The absence of a Book 3 event in the limited log output does not establish whether the application rejected the operation through authorization logic, request validation, or another mechanism.

#### Step 89B: Inspect the HTTP Response Body

To investigate the HTTP 400 response directly, I returned to Microsoft Edge Developer Tools.

Navigation:

**DevTools → Network → Request `3` → Response**

The objective is to inspect the response body returned by Calibre-Web NextGen.

I will look for evidence of:

- CSRF validation failure.
- Invalid or missing request data.
- Authentication or authorization rejection.
- An application error or other validation condition.

#### Step 89C: Preserve the Evidence

I will record the response status and relevant response message without including session cookies or CSRF tokens in the public GitHub documentation.

The response body may help identify the failure mechanism, but server-side state verification will still be necessary before confirming or dismissing the authorization hypothesis.

### Outcome

The latest logs showed only authorized Book ID 2 read-status events, while the manipulated Book ID 3 request returned HTTP 400.

**The cause of the HTTP 400 remains undetermined. Candidate #1 is still unresolved.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/28a5571a431af0cfa00e73d2e63da45e8ea3cb00/Screenshot%202026-09-27%20005000.png)


### Step 90: Compare Legitimate and Manipulated HTTP Request Headers

During Candidate #1 — Read-Status Object Authorization, the manipulated request targeting restricted Book ID 3 returned HTTP 400.

The Response tab displayed a generic HTTP 400 message without identifying the underlying cause.

Rather than continuing to modify the request through trial and error, I decided to compare the successful control request against the failed manipulated request.

#### Step 90A: Inspect the Legitimate Request

While authenticated as `lab5-user-a`, I navigated to:

**Microsoft Edge → DevTools → Network → Request `2` → Headers**

The legitimate control request was:

```http
POST /ajax/toggleread/2
```

Previously observed response: `200 OK`

I will examine the Request Headers section, focusing on:

- **Content-Type:** Determines how the request body is encoded.
- **Cookie:** Identifies whether the authenticated session is being transmitted.
- **Origin:** Identifies the origin initiating the request.
- **Referer:** Provides the referring page context.
- **Content-Length:** Indicates the size of the submitted request body.

I will also inspect **Payload → Form Data** to confirm the request body structure.

#### Step 90B: Compare Against the Manipulated Request

The manipulated request targeted:

```http
POST /ajax/toggleread/3
```

Observed response: `400 Bad Request`

I will compare the two requests to identify differences beyond the target book ID.

This comparison will help determine whether the HTTP 400 resulted from request formatting, CSRF validation, session handling, or another application condition.

#### Step 90C: Protect Authentication Data

Before documenting or sharing the headers, I will redact:

- Session cookie values.
- CSRF token values.
- Any other authentication credentials.

I will preserve header names and non-sensitive information needed for the comparison.

### Outcome

The HTTP 400 response did not provide a specific explanation. I selected a direct comparison of the successful and manipulated HTTP requests as the next diagnostic step.

**Candidate #1 remains unresolved.** No authorization bypass has been confirmed.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/829f1dc5f55c00500938d2dc3b7dea6ebc2e08b2/Screenshot%202026-09-27%20005245.png)


### Step 91: Reproduce the Legitimate Request Headers for Candidate #1

During Candidate #1 — Read-Status Object Authorization, I compared the successful Book ID 2 request with the manipulated Book ID 3 request.

The legitimate request included two important HTTP headers:

```http
X-Csrftoken: [REDACTED]
X-Requested-With: XMLHttpRequest
```

It also included the CSRF token in the URL-encoded request body.

My previous `fetch()` attempt included the CSRF form field but did not reproduce both headers.

This difference may explain the HTTP 400 response, although the cause has not yet been confirmed.

#### Step 91A: Prepare the Corrected Request

While authenticated as `lab5-user-a`, I prepared the following command in Microsoft Edge Developer Tools → Console:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',headers:{'X-CSRFToken':x.get('csrf_token'),'X-Requested-With':'XMLHttpRequest'},body:x})
```

The existing `x` object contains the CSRF token obtained from the legitimate request.

The command is designed to preserve:

- Authenticated browser session: `lab5-user-a`
- HTTP method: `POST`
- CSRF token in the request body
- CSRF token in the `X-CSRFToken` header
- `X-Requested-With: XMLHttpRequest`
- URL-encoded form submission

The security-relevant target remains restricted Book ID 3.

#### Step 91B: Verify the Request Format

The authorized control endpoint is:

```http
POST /ajax/toggleread/2
```

The manipulated endpoint is:

```http
POST /ajax/toggleread/3
```

The objective is to reproduce the legitimate request as closely as possible while changing the target book ID.

I will inspect the actual transmitted headers and payload to verify that the browser generated the intended request.

#### Step 91C: Collect the Result

After submitting the manipulated request once, I will examine:

1. HTTP response status.
2. Response body.
3. Relevant application logs.
4. Whether Book ID 3's read-status state changed.

A successful HTTP response alone will not confirm an authorization vulnerability. Independent server-side verification is required.

### Outcome

The request-header comparison identified two headers missing from the earlier manipulated request.

A corrected request has been prepared to test whether Calibre-Web NextGen permits a standard user to modify the read status of a book hidden by a denied-tag restriction.

**Candidate #1 remains unresolved pending execution and verification.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/db5f754730f8a3da0d259d562895ebbb289a4980/Screenshot%202026-09-27%20010848.png)


### Step 92: Investigate the HTTP 400 Response Using Container Logs

During Candidate #1 — Read-Status Object Authorization, I submitted another manipulated request targeting restricted Book ID 3.

This attempt included the CSRF token in both the form body and the `X-CSRFToken` request header, along with `X-Requested-With: XMLHttpRequest`.

The browser confirmed that the request executed, but Calibre-Web NextGen returned:

```http
POST /ajax/toggleread/3
400 Bad Request
```

#### Step 92A: Verify the HTTP Method

The JavaScript request used the Fetch API, and the browser displayed the transmitted method as `POST`.

Although HTTP method tokens are case-sensitive by specification, the Fetch API normalizes recognized standard methods such as `POST`.

Therefore, method capitalization does not explain this particular HTTP 400 response.

#### Step 92B: Inspect the Application Logs

To identify the server-side reason, I returned to PowerShell and prepared the following command:

```powershell
docker logs calibre-web-nextgen --tail 5
```

The objective is to examine the latest application messages associated with the failed request.

#### Step 92C: Interpret the Evidence

**If the logs report an expired or invalid CSRF token:**

I will generate a fresh authorized control request and immediately compare its CSRF header and form-body values against the manipulated request.

**If the logs do not report a CSRF error:**

I will investigate other application validation or authorization checks. The absence of a CSRF message in five log lines will not, by itself, establish that CSRF validation succeeded.

#### Step 92D: Maintain the Authorization Assessment

The comparison remains:

- Authorized Book ID 2: previously returned HTTP 200.
- Restricted Book ID 3: returned HTTP 400.
- Authenticated account: `lab5-user-a`.
- Candidate vulnerability: object-level authorization on the read-status endpoint.

The cause of the latest HTTP 400 response has not yet been confirmed.

### Outcome

The manipulated request executed and returned HTTP 400 despite including the previously identified CSRF and XMLHttpRequest headers.

**Candidate #1 remains unresolved.** Further server-side evidence is required before determining whether the application enforces object-level authorization.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/fc7576133f4e6a47c1392f6a143904aa76ad1103/Screenshot%202026-09-27%20125018.png)


### Step 93: Inspect the Latest Manipulated Request in DevTools

During Candidate #1 — Read-Status Object Authorization, I reviewed the Calibre-Web NextGen container logs following the latest manipulated request.

No new CSRF validation error appeared in the retrieved log output.

However, this does not conclusively establish that CSRF validation succeeded. The request still returned HTTP 400, and the underlying cause remains unidentified.

#### Step 93A: Locate the Latest Request

I returned to Microsoft Edge Developer Tools and navigated to:

**Network → Fetch/XHR**

I selected the newest request named `3`, corresponding to the most recent attempt against restricted Book ID 3.

I then opened:

**Headers → General**

#### Step 93B: Examine the HTTP Request Details

I will inspect three fields:

- **Request URL:** Confirm that the request targeted `http://localhost:8083/ajax/toggleread/3`.
- **Request Method:** Confirm that the browser transmitted `POST`.
- **Status Code:** Confirm the actual HTTP response returned by Calibre-Web NextGen.

These details will establish which request was submitted and its observed HTTP outcome.

#### Step 93C: Preserve Evidence

I will capture a screenshot of the General section for my GitHub research documentation.

The screenshot should show the request URL, method, and status code without exposing session cookies or CSRF tokens.

#### Step 93D: Continue the Investigation

After confirming the request details, I will compare its transmitted headers and payload against the successful Book ID 2 control request.

The objective is to identify any remaining differences before attributing the HTTP 400 response to authorization enforcement.

### Outcome

The latest container log output did not reveal a new CSRF error. The manipulated request's HTTP 400 response remains unexplained.

**Candidate #1 remains unresolved.** No authorization vulnerability has been confirmed.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/83b9e490f229dee39f894435ec5004fd7d624687/Screenshot%202026-09-27%20125457.png)


### Step 94: Inspect the HTTP 400 Response for Restricted Book ID 3

During Candidate #1 — Read-Status Object Authorization, I confirmed that the latest manipulated request reached Calibre-Web NextGen.

#### Step 94A: Verify the Request Details

Microsoft Edge Developer Tools displayed:

```http
Request URL: http://localhost:8083/ajax/toggleread/3
Request Method: POST
Status Code: 400 BAD REQUEST
```

This confirms that the browser submitted the request to the intended endpoint and received an HTTP response.

However, the HTTP 400 status alone does not establish whether the application rejected the request because of authorization, CSRF validation, or another condition.

#### Step 94B: Inspect the Server Response

To investigate the failure, I kept the newest request named `3` selected in the Network panel.

I navigated to:

**DevTools → Network → Request `3` → Response**

The objective is to inspect the response body for an application-generated explanation of the HTTP 400 status.

I will look for:

- CSRF validation errors.
- Missing or invalid request data.
- Authorization-related messages.
- Other application validation failures.

#### Step 94C: Document the Evidence

I will record the response body and compare it against the application's server logs and the successful Book ID 2 control request.

If the response is generic, I will document that limitation rather than assume the reason for rejection.

### Outcome

The manipulated POST request to restricted Book ID 3 returned HTTP 400.

The request URL, method, and status code are verified, but the cause of the failure remains undetermined.

**Candidate #1 remains unresolved.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/65b5244e2ba090f64e7bb2710813ef36e1a2932b/Screenshot%202026-09-27%20010848.png)


### Step 95: Compare the Successful Control Request Headers

During Candidate #1 — Read-Status Object Authorization, I inspected the HTTP response from the latest manipulated request targeting restricted Book ID 3.

The application returned a generic HTTP 400 error page without explaining the cause.

The latest container log output also did not show a new CSRF validation error. However, this does not establish that CSRF validation succeeded.

Rather than submitting additional requests, I decided to compare the successful control request against the failed manipulated request.

#### Step 95A: Locate the Successful Control Request

While authenticated as `lab5-user-a`, I navigated to:

**Microsoft Edge → DevTools → Network → Request `2` → Headers**

I selected the successful request:

```http
POST http://localhost:8083/ajax/toggleread/2
```

**Previously observed response:** `200 OK`

#### Step 95B: Inspect the Request Headers

I scrolled to the **Request Headers** section to examine:

- `Content-Type` — Request body encoding.
- `Origin` — Origin of the request.
- `Referer` — Page associated with the request.
- `X-CSRFToken` — CSRF token submitted in the request header.
- `X-Requested-With` — Header identifying the AJAX request.

I will compare these values against the corresponding headers in the failed Book ID 3 request.

#### Step 95C: Preserve the Evidence

I will capture a screenshot of the successful request's headers.

Before including the screenshot in my public GitHub documentation, I will redact the CSRF token value, session cookies, and other authentication secrets.

#### Step 95D: Identify Remaining Request Differences

The objective is to determine whether the manipulated request differs from the successful control in any relevant way beyond the target book ID.

This comparison will help identify request-formatting or validation differences before investigating authorization behavior.

### Outcome

The manipulated Book ID 3 request returned a generic HTTP 400 response, and the available logs did not identify its cause.

I selected a direct comparison of the successful and failed request headers as the next diagnostic step.

**Candidate #1 remains unresolved. No object-level authorization bypass has been confirmed.**

![mage alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2f6412422d8df37321d546c613612da44db29b54/Screenshot%202026-09-27%20011204.png)


### Step 96: Verify the Content-Type Header of the Successful Control Request

During Candidate #1 — Read-Status Object Authorization, I continued examining the successful HTTP 200 request for Book ID 2.

#### Step 96A: Confirm the Existing Request Headers

The legitimate request contained:

```http
POST /ajax/toggleread/2
Origin: http://localhost:8083
Referer: http://localhost:8083/book/2
X-CSRFToken: [REDACTED]
X-Requested-With: XMLHttpRequest
```

These headers establish the request's origin, referring page, CSRF header, and AJAX request identifier.

#### Step 96B: Locate the Content-Type Header

I remained in:

**DevTools → Network → Request `2` → Headers → Request Headers**

I scrolled upward to locate the `Content-Type` header.

The objective is to confirm the exact content type used by the successful request and compare it against the failed Book ID 3 request.

The previously observed legitimate request used:

```http
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
```

I will verify whether the current successful control request uses the same value.

#### Step 96C: Continue the Request Comparison

After confirming Content-Type, I will compare the successful and failed requests without submitting another manipulated request.

The comparison will focus on identifying differences in request headers and form data beyond the target book ID.

### Outcome

The successful control request's Origin, Referer, CSRF header, and XMLHttpRequest header were identified.

**Content-Type verification is pending. Candidate #1 remains unresolved.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5d7528edea6a2b32bfc5e2929984ded24af7ec43/Screenshot%202026-09-27%20011400.png))


### Step 83: Prepare the CSRF-Preserving Read-Status Authorization Test

During Lab 5 dynamic testing, I identified another difference between the successful authorized request and my earlier JavaScript Console attempts.

The legitimate read-status request used the following header:

```http
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
```

My earlier `fetch()` request did not explicitly reproduce this content type or include the required CSRF data.

As a result, the earlier HTTP 400 response could not establish whether object-level authorization was enforced.

#### Step 83A: Reproduce the Legitimate Request Format

While authenticated as `lab5-user-a`, I prepared the following request in Microsoft Edge Developer Tools → Console:

```javascript
fetch('/ajax/toggleread/3',{method:'POST',headers:{'Content-Type':'application/x-www-form-urlencoded; charset=UTF-8','X-CSRFToken':x.get('csrf_token'),'X-Requested-With':'XMLHttpRequest'},body:x.toString()})
```

This request is designed to:

- Preserve the authenticated browser session.
- Use HTTP `POST`.
- Submit URL-encoded form data.
- Include CSRF information from the existing variable `x`.
- Identify the request as XMLHttpRequest.
- Target restricted Book ID 3.

**Prerequisite:** The variable `x` must already contain the legitimate request's form data, including a valid `csrf_token`. Otherwise, the command will fail or may not reproduce the authorized request.

#### Step 96C: Isolate the Authorization Variable

The authorized control request targeted:

```http
POST /ajax/toggleread/2
```

The manipulated request targets:

```http
POST /ajax/toggleread/3
```

The goal is to preserve the successful request format while changing only the target book ID.

This comparison will be valid only if the CSRF token, request body, relevant headers, and authenticated session match the successful control request.

#### Step 83C: Collect the Response Evidence

After executing the request once, I will inspect:

1. The HTTP response status in the Network tab.
2. The response body.
3. Relevant Calibre-Web NextGen container logs.
4. Whether Book ID 3's read-status state changed.

A successful HTTP response will require independent server-side state verification before I can determine whether an authorization vulnerability exists.

### Outcome

I identified the legitimate request's URL-encoded content type and prepared a CSRF-preserving test targeting restricted Book ID 3.

**The request has not yet been executed, and Candidate #1 remains unresolved.**

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/791b312db92a9978d96e56f32c3894723185dbe1/Screenshot%202026-09-27%20155017.png)


### Step 97: Recover the Docker Environment and Pin the Application Version

During Lab 5 dynamic testing, the Docker environment experienced storage-related issues that interrupted the ongoing object-level authorization assessment.

To restore a consistent testing environment, I rebuilt the Calibre-Web NextGen container and pinned the application to **v4.1.43**, matching the version used during Lab 4 static analysis.

#### Step 97A: Identify the Environment Issue

The storage-related interruption affected the Docker testing environment and prevented me from continuing the authorization investigation under consistent conditions.

I paused testing to address the environment before collecting additional security evidence.

#### Step 97B: Rebuild the Application Container

I rebuilt the Calibre-Web NextGen container to restore the dynamic testing environment.

I also replaced the previously unpinned application image configuration with a version-specific reference targeting **v4.1.43**.

This was intended to maintain consistency between the application version examined during static analysis and the version used for dynamic testing.

#### Step 97C: Maintain Research Consistency

Using the same application version across both labs is important because application updates may change:

- Authentication and authorization logic.
- CSRF validation behavior.
- Endpoint implementations.
- Database operations.
- Security controls relevant to the vulnerability candidates.

Pinning the version reduces the risk of investigating behavior introduced by a different application release.

#### Step 97D: Prepare to Resume Authorization Testing

Before continuing Candidate #1, I will verify that:

1. The rebuilt container is running and healthy.
2. The deployed application reports v4.1.43.
3. The synthetic test books remain available or are recreated.
4. The standard test account and restricted-tag permissions are correctly configured.
5. The authorized read-status request still works.

I will then resume the CSRF-preserving authorization test against restricted Book ID 3.

### Outcome

The Calibre-Web NextGen container was rebuilt following a storage-related interruption, and the application configuration was pinned to v4.1.43.

Runtime version verification and confirmation of the restored test data remain necessary before continuing the authorization assessment.

**Candidate #1 remains unresolved.** No object-level authorization vulnerability has been confirmed.

