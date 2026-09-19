# Dynamic-Testing
Testing a running application by interacting with its features and manipulating controlled requests to determine whether potential vulnerabilities identified during static analysis can actually be reproduced and have a security impact.

**Prepare the Dynamic Testing Environment**

Before manipulating requests, we need to establish exactly what version of the application we're testing and make sure the target repository hasn't changed since our static analysis.

Your Lab 4 baseline was:

Target: Calibre-Web NextGen
Version: 4.1.43
Commit: 7e9221f455329bb3e6611b4652ac23b7f8629bb0
Branch: main

Open PowerShell in: git -C .\targets\Calibre-Web-NextGen status --short

We want to make sure the target source code itself has not been modified before dynamic testing.

If nothing appears, that's a clean working tree.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/97bf0a3d69e8a973cc74209b3e5e690070ef3215/Screenshot%202026-09-17%20092356.png)

That means the Calibre-Web NextGen target repository has no modified or untracked files.

Verify the exact target commit

Run: git -C .\targets\Calibre-Web-NextGen rev-parse HEAD

We expect: 7e9221f455329bb3e6611b4652ac23b7f8629bb0

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/35a979ca0bc7bb66ea31c9a98eb464a94947fc68/Screenshot%202026-09-17%20092345.png)

That matches our Lab 4 baseline exactly:

7e9221f455329bb3e6611b4652ac23b7f8629bb0

Target commit: VERIFIED

Verify the target branch

Run: git -C .\targets\Calibre-Web-NextGen branch --show-current

We expect: main

Branch: main 
Commit: previously verified 
Working tree: clean 

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/27a78aebd5813135ec1488ff1384d945ff61fbf0/Screenshot%202026-09-17%20092826.png)

The Lab 5 baseline is matching Lab 4.

Verify the application version

Run: Select-String -Path .\targets\Calibre-Web-NextGen\cps\constants.py -Pattern "STABLE_VERSION"

We’re expecting the version associated with our baseline: 4.1.43

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a4d3fa4f575f4a5548816fe36d0e89af1cba468e/Screenshot%202026-09-17%20093417.png)

No output means STABLE_VERSION isn't defined in that file under that exact name. Nothing is wrong—we just searched for the wrong constant.

Let's locate where 4.1.43 is defined in the target source.

Run: Get-ChildItem .\targets\Calibre-Web-NextGen\cps -Recurse -File | Select-String -Pattern "4\.1\.43" | Select-Object -First 10

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5d345134ab50ded4085c0ab361ac7eec81d9f393/Screenshot%202026-09-17%20093645.png)

That also returned no results. So 4.1.43 is not stored as a literal string anywhere under the cps directory at this commit. That's okay—the exact Git commit is the stronger reproducibility identifier anyway, and we've already verified it.

Rather than spend more time chasing a version string, we'll record:

Target commit: 7e9221f455329bb3e6611b4652ac23b7f8629bb0
Branch: main
Working tree: Clean

Check how the application is launched

Run: Get-ChildItem .\targets\Calibre-Web-NextGen | Select-Object Name

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/20b43b8dcd4051b7a584459a51609ba2d8a28745/Screenshot%202026-09-17%20093828.png)

We found two important files:

cps.py — likely the Python application entry point.
VERSION — this is probably where the project stores its version. That explains why our earlier search under cps returned nothing.
Verify the version

Run: Get-Content .\targets\Calibre-Web-NextGen\VERSION

After that, we'll move to starting the local application for dynamic testing.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/48627edfb05f26d35360a211477d4d0f86132331/Screenshot%202026-09-17%20094042.png)

That confirms the complete Lab 5 testing baseline:

Calibre-Web NextGen: 4.1.43 
Branch: main 
Commit: 7e9221f455329bb3e6611b4652ac23b7f8629bb0 
Working tree: Clean 

Activate your research environment

Before we attempt to launch the application, activate your existing virtual environment: .\.venv\Scripts\Activate.ps1

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b917224140e49cc5985e38284c0e7efc2fa45c6f/Screenshot%202026-09-17%20094417.png)

That's the same PowerShell execution-policy restriction we encountered earlier. Your .venv itself isn't broken.

Temporarily allow the activation script

Run this first: Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

Then run: .\.venv\Scripts\Activate.ps1

We want the prompt to change to: (.venv) PS C:\Users\eelve\Vulnerability-Research-Lab>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/028f974aee69bccf74d43e0ffd2a2d0bed1e65b6/Screenshot%202026-09-17%20094721.png)

The screenshot confirms the virtual environment is active:

(.venv) PS C:\Users\eelve\Vulnerability-Research-Lab>

Check the application's startup options

Before launching anything, let's see what this exact version expects.

Run: python .\targets\Calibre-Web-NextGen\cps.py --help

Then we'll use the correct startup command for your local testing environment.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4fb21783450b1402ff962932d9327a1433a780ad/Screenshot%202026-09-17%20094933.png)

this tells us exactly what's wrong. The application reached its Python code, but your research .venv doesn't currently have Flask installed:

ModuleNotFoundError: No module named 'flask'

That's a dependency/environment issue, not a Calibre-Web vulnerability or application failure.

**Find the project's dependency configuration**

Since this repository has pyproject.toml, let's inspect how its dependencies are defined before installing anything.

Run: Select-String -Path .\targets\Calibre-Web-NextGen\pyproject.toml -Pattern "dependencies|flask" -Context 2,5

We'll install dependencies using the method this repository expects, rather than manually installing Flask and potentially missing dozens of other required packages.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8873d733a065ab660fad60294b6bb545e6721da/Screenshot%202026-09-17%20095155.png)

Perfect. This confirms the project declares its runtime dependencies directly in pyproject.toml, including Flask and the authentication-related packages we’ll eventually need.

For Lab 5 — Step 1: Prepare the Dynamic Testing Environment, the next move is to install the target itself into your already-active .venv. Since we want to preserve the source checkout and install exactly from the local authorized target. 

run only: python -m pip install -e .\targets\Calibre-Web-NextGen

The -e performs an editable local install, so Python uses your cloned Calibre-Web NextGen source while installing the dependencies declared in pyproject.toml.

This may install quite a few packages and could take several minutes. Do not start the application yet.

![Inage alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/18665aca2ed3554c78e7b1ba219d8d4e0340f4c1/Screenshot%202026-09-17%20100759.png)

The install did not complete, and the important part is:

Failed to build faust-cchardet python-ldap

Several other packages built successfully, so we don't need to restart blindly. These two dependencies commonly involve native/compiled components, so first we should inspect the actual build error rather than changing your environment.

Lab 5 — Step 1A: Diagnose python-ldap

Run only this: python -m pip install python-ldap -v

Capture the last 20–30 lines around the first actual compiler/error message, especially anything mentioning:

error:
Microsoft Visual C++
cl.exe
lber.h
ldap.h


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d9c9cc19bf1268c7b2cddd4b7f8624dc17f4cb4d/Screenshot%202026-09-17%20101146.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4a822239a08e8f6256507fa1ae0970a78d57b739/Screenshot%202026-09-17%20101203.png)

That confirms python-ldap is the blocker, but this screenshot still only shows pip’s final summary. The actual compiler error occurred earlier in the output.

Before installing Visual C++ tools or changing Python versions, I want the exact cause.

Save the full error

Run: python -m pip install python-ldap -v *> .\python-ldap-build.txt

Then run: Select-String -Path .\python-ldap-build.txt -Pattern "fatal error|error C|ldap.h|lber.h|cl.exe|Microsoft Visual C" -Context 3,3

Then we'll know whether we're dealing with missing C++ build tools, missing LDAP development headers/libraries, or another Python 3.13 compatibility issue. Don't install anything else yet.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a1cef7e4bb4eb2c1ac5babd273801aafe89b70a4/Screenshot%202026-09-17%20101407.png)

now we have the exact cause.

Your log says:

error: Microsoft Visual C++ 14.0 or greater is required.

Because python-ldap is specifically for the optional LDAP authentication feature, we can first determine whether Calibre-Web NextGen can run locally without LDAP support. Candidate #4 can be handled separately later if we need an LDAP-capable environment.

check the project's startup instructions

From your current project directory, run: Get-ChildItem .\targets\Calibre-Web-NextGen -Name | Select-String "README|requirements|setup|docker"

We'll use the project's supported installation/startup method rather than forcing a 7 GB compiler installation onto your machine.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/8a445e77ebe82afdaa1d93b9711ce053f4f12922/Screenshot%202026-09-17%20103709.png)

That confirms the repository provides both Docker and regular installation documentation. Since disk space is already tight, I don't want to have you install Docker Desktop—it could consume even more space.

Check the README installation instructions

Run: Select-String -Path .\targets\Calibre-Web-NextGen\README.md -Pattern "install|Windows|pip|Docker" -Context 3,6

Then we'll choose the smallest supported installation path that lets us dynamically test Candidates #1, #2, #5, and #6 without installing 7 GB of Visual Studio tooling. We can revisit the LDAP-specific candidate separately.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a6cfe80198240bcb84a42a583f1eda6200b33700/Screenshot%202026-09-17%20103937.png)

I reviewed the output. It gives us an important answer: the project's documented Quick Start is Docker-based—it explicitly lists Docker and Docker Compose as requirements. It then starts the application with docker compose up -d and expects it at localhost:8083.

Since I don't have enough disk space for the ~7 GB Visual C++ installation, I don't want to send you down the Docker route either without checking storage first.

Check available disk space

Run only: Get-PSDrive C | Select-Object Used,Free

Once I see exactly how much free space you have, we'll choose the lowest-storage path for getting the local target running

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/51a05c4c82922567a1d9482f59c31343800ccda4/Screenshot%202026-09-17%20104140.png)

I currently have 50,713,722,888 bytes free ≈ 47.2 GB on C:.

So the issue wasn't that my computer lacked 7 GB total I have plenty of free space. The Visual Studio installer likely wanted roughly 7 GB for the selected workload, and you understandably didn't want to spend that much just for one dependency.

For this lab, I'd still avoid that large installation until we know it's necessary. Let's see whether Windows already has Docker or Podman installed, since the project's README supports those approaches.

Check existing container tools

Run: docker --version
podman --version

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c214a14336f548eb66b5a96deb415e0edb9d742/Screenshot%202026-09-17%20104558.png)

neither Docker nor Podman is installed. So we have three potential routes, and installing Docker just to solve this would add unnecessary overhead.

The best next move is to determine whether the application already supports disabling optional LDAP functionality. If it does, we may be able to run the other Lab 5 tests using your existing Python environment without python-ldap.

Check how LDAP is imported

Run only: Select-String -Path .\targets\Calibre-Web-NextGen\cps\*.py -Pattern "import ldap|from ldap|flask_simpleldap|SimpleLDAP" -Context 2,3

We're specifically checking whether LDAP is conditionally imported. If it is, we'll try to bypass that optional dependency 
cleanly rather than installing several gigabytes of development tools.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/90f13224abf8ee5546af2f89d11791704dced89d/Screenshot%202026-09-17%20104752.png)

This result is useful, but it doesn't show an LDAP import. It only found the word LDAP in web.py, where the application calls its LDAP service:

ldap_user_details = services.ldap.get_object_details(username)

That suggests the actual python-ldap import may live deeper under cps\services, which our cps\*.py search didn't recursively inspect.

Lab 5 — Step 1G: Search recursively

Run this one command: Get-ChildItem .\targets\Calibre-Web-NextGen\cps -Recurse -Filter *.py | Select-String -Pattern "import ldap|from ldap|flask_simpleldap|SimpleLDAP" -Context 2,3

This should tell us whether LDAP support is conditionally loaded. If it is, we may be able to run the application without 
python-ldap and postpone Candidate #4 — LDAP fallback until later.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8dc5a01f3a5ac979fdcacdb80a305d67e7da484/Screenshot%202026-09-17%20104932.png)

Excellent. This gives us the answer we were looking for.

cps\services\__init__.py deliberately handles the missing LDAP module:

try:

    from . import simpleldap as ldap
    
    from .simpleldap import ldapVersion
    
except ImportError as err:

    log.debug("Cannot import simpleldap, logging in with ldap will not work: %s", err)
    
    ldap = None
    
    ldapVersion = None

So LDAP is optional at runtime. We can postpone Candidate #4 and avoid installing the ~7 GB C++ build environment.

The remaining blocker from the original installation was faust-cchardet. Let's determine its exact failure before changing anything.

Diagnose faust-cchardet

Run: python -m pip install faust-cchardet -v *> .\faust-cchardet-build.txt

Then: Select-String -Path .\faust-cchardet-build.txt -Pattern "fatal error|error C|Microsoft Visual C|Failed|ERROR" -Context 3,3

If it also only fails because it wants the C++ compiler, we'll look at bypassing that optional dependency rather than installing the large toolchain.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ae7ec2996c3933caf7dffb5378e142373611d76a/Screenshot%202026-09-17%20105315.png)

The command returned no matches, which means faust-cchardet did not fail with the same obvious Visual C++ error pattern we saw for python-ldap.

Run: Get-Content .\faust-cchardet-build.txt -Tail 40

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/34ed439b886db0c4d363171567d6f1c064537472/Screenshot%202026-09-17%20110110.png)

faust-cchardet is resolved. This output confirms:

Successfully installed faust-cchardet-3.2.0

So the only known failed dependency now is python-ldap, and we already confirmed the application handles LDAP as optional when that import isn't available.

erify the core environment

Now run only: python -c "import flask, sqlalchemy; print('Flask:', flask.__version__); print('SQLAlchemy:', sqlalchemy.__version__)"

If that passes, we'll continue toward getting Calibre-Web NextGen running locally without installing the 7 GB Visual C++ 
package.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ff5fe89255c860e0b2e16236c3a2825ebf69ce46/Screenshot%202026-09-17%20110322.png)

This tells us something important: the failed editable install rolled back before installing Flask, so we don't yet have a runnable core environment.

We should not manually install Flask alone because Calibre-Web NextGen has many dependencies, and that could leave us with an inconsistent environment.

Since faust-cchardet is now successfully installed and python-ldap is the remaining known blocker, let's check whether pip can install the project while skipping dependency resolution. First, though, we need to see which dependencies are already present.

Check installed packages

Run: python -m pip list

![iamge alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/adbbc4b561287613263653157a10c216d694d2e8/Screenshot%202026-09-17%20110510.png)

This confirms the environment currently contains your research tooling and faust-cchardet, but not the Calibre-Web runtime dependencies such as Flask and SQLAlchemy.

Rather than manually installing dozens of packages, we can have pip resolve the target normally while telling it to skip only python-ldap. A clean way is to use a pip constraint that makes LDAP unavailable—but before doing that, I want to inspect whether the project already provides a supported non-LDAP dependency group.

Inspect dependency definitions

Run this: Get-Content .\targets\Calibre-Web-NextGen\pyproject.toml | Select-Object -Skip 30 -First 90

We're looking at the entire runtime dependency block, not just Flask/LDAP. Then I can give you one controlled installation command that installs everything needed for the local application while avoiding the native python-ldap build.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/251855a2701af756853d92da4518c65c1327b673/Screenshot%202026-09-17%20110708.png)

This confirms there is no separate non-LDAP dependency group. It also reveals something important about faust-cchardet: the project requires >=2.1.18,<2.1.20, while we installed 3.2.0 manually. So we should not treat 3.2.0 as satisfying the target's declared environment.

Rather than modifying pyproject.toml or forcing incompatible versions, let's check whether the repository's AI_README.md provides a supported bare-metal/Windows setup. The main README specifically says that file is a setup guide intended for an AI assistant to follow.

Step 1K

Run only: Get-Content .\targets\Calibre-Web-NextGen\AI_README.md -First 120

We'll follow the project's own setup guidance if it provides a lower-storage route. I don't want to alter the target's dependency constraints just to make the installation succeed, because maintaining the target's intended configuration matters for valid vulnerability research.

If WSL2 is already available, we'll determine how much additional installation is actually necessary before downloading Docker Desktop.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1965acfd6e3ad7ebb00e93446b759faa9b340745/Screenshot%202026-09-17%20110926.png)

This settles it. The project's own setup guide says that on Windows, Docker Desktop + WSL2 is the supported setup, and that if Docker isn't installed, it is a prerequisite. So continuing to fight the bare-metal Python dependencies would move us away from the target's intended runtime.

Because you have about 47 GB free, Docker may still be feasible, but before downloading anything we should check whether WSL2 is already installed.

Check WSL

Run only: wsl --status

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d74e1eae4bf30e8f19eaab56ecc8d157aaf587fa/Screenshot%202026-09-17%20111128.png)

WSL2 is already enabled, and Default Version: 2 is exactly what we wanted. The kernel is older, but we don't need to change anything yet.

Before considering Docker Desktop, let's check whether you already have a Linux distribution installed under WSL.

Run only: wsl --list --verbose

If Ubuntu or another Linux distribution is already installed and shows VERSION 2, that removes another setup requirement. Don't run wsl --update or install Docker yet.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0aeaa48334f76f493a7167d4ff7f76613086671d/Screenshot%202026-09-17%20111432.png)

That confirms the situation:

WSL2: installed 

Default WSL version: 2 

Linux distribution: not installed 

Docker: not installed 

Since the project's Windows instructions call for WSL2 + Docker Desktop, the next smallest step is installing a Linux distribution. Ubuntu itself is much smaller than the 7 GB Visual Studio workload.

See available WSL distributions

Run only: wsl --list --online

We'll choose the appropriate Ubuntu version from Microsoft's available list before installing anything.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/81941459986962842b87fee27b01d0ff0612825b/Screenshot%202026-09-17%20111635.png)

Ubuntu is available, so we can continue with the project's supported Windows path.

Install Ubuntu for WSL2

Run: wsl --install -d Ubuntu

This will download and install Ubuntu. It may ask you to restart Windows. If it does, restart before continuing.

After installation, Ubuntu may open and ask you to create a Linux username and password. These are just for your local Ubuntu environment; they do not need to match your Windows credentials.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0c9bf4917da5bb340d50c72bba1d921a56bec6a6/Screenshot%202026-09-17%20112003.png)

Ubuntu installed successfully. The important lines are:

Ubuntu has been installed.

Launching Ubuntu...

I'm currently logged in as root, which is okay for this temporary research environment. Notice I'm currently under /mnt/c/...; the project's instructions recommend keeping its Docker runtime data inside the WSL filesystem, so we'll follow that when we get there.

Verify WSL from Windows

First exit Ubuntu: exit

Once you're back at your PowerShell prompt, run: wsl --list --verbose

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/08ee2fbce534724ce63eff24d4ff9151a9ea72b9/Screenshot%202026-09-17%20210557.png)

Verification passed:

NAME      STATE     VERSION
* Ubuntu  Stopped   2

So Ubuntu is installed correctly and configured as WSL2. Stopped is normal because you exited the Ubuntu session.

 **Step 1P: Docker Desktop**

We can now move to the project's documented Windows runtime approach. Install Docker Desktop for Windows from the official Docker site:

Click on download
![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d495d55eaccf280dfac3b7cd4d9085d851ac8929/Screenshot%202026-09-17%20210740.png)

Select the version for your computer

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/833af0bd1d4195da937212a925d7cb4f6291fc75/Screenshot%202026-09-17%20210908.png)

Select your configurations and what for Docker to install

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5671c61cdf9112088b7621545ffa3a80c3854ad6/Screenshot%202026-09-18%20184257.png)

During installation, keep the WSL 2 backend option enabled. You do not need Hyper-V if the installer offers WSL2 as the backend.

After installation, launch Docker Desktop and wait until it reports that the Docker engine is running.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c6a94fb37d1678cae6697e1021809f3717264f5/Screenshot%202026-09-18%20190940.png)

**Configure Docker storage on D:**

Click the ⚙️ Settings icon near the top-right.


![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6cf3a4507f72169ef3ee949e2501fcc2488ac0d9/Screenshot%202026-09-18%20220643.png)

Then look for Resources → Advanced.

We want the setting called something like Disk image location.

Select so Docker's large images/containers live on:

D:\Vulnerability-Research-Docker

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f05ad7084496e96abaf5f02413db0884b3394a6a/Screenshot%202026-09-18%20222320.png)

Confirm that Disk image location now points to your D: drive, then click Apply & restart.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/418f6be8a966d2aa389995acb5f2a6c21bb8f7cf/Screenshot%202026-09-18%20222750.png)


Docker will restart and move its disk image. Keep the Toshiba drive connected during this process.

Once Docker comes back and shows Engine running. I'll verify the storage location before pulling Calibre-Web NextGen.



This confirms Docker Engine is running after the storage move. It also shows a disk limit of about 1006.85 GB, so Docker now has substantial storage available instead of being constrained by C:

**Prepare Dynamic Testing Environment.**

Before pulling Calibre-Web NextGen, let's verify from PowerShell that Docker itself is healthy.

Run: docker info --format "Docker Root Dir: {{.DockerRootDir}} | Server Version: {{.ServerVersion}}"

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/19982429774520746b6ec7fc7404586efdcc8042/Screenshot%202026-09-18%20223447.png)

That confirms the Docker backend is responding correctly:

Docker Root: /var/lib/docker

Server Version: 29.8.0

Because Docker Desktop's WSL disk image was moved to D:\Vulnerability-Research-Docker\DockerDesktopWSL, /var/lib/docker is the normal Linux-side path inside that disk. We're ready to proceed.

**Calibre-Web NextGen setup**

Let's first check whether the target repository already contains a Docker Compose file. From:

C:\Users\eelve\Vulnerability-Research-Lab

run: Get-ChildItem .\targets\Calibre-Web-NextGen -Filter "*compose*" | Select-Object Name

We'll use the target project's own supported configuration rather than inventing one.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/15b8b1151bf07480f996ed778eb3d5770a3b68f8/Screenshot%202026-09-18%20223748.png)

confirms the repository includes both:

docker-compose.yml — normal deployment
docker-compose.yml.dev — development configuration

For our controlled Lab 5 dynamic testing, let's inspect the normal Compose configuration before running it. This lets us verify the image, ports, volumes, and environment settings first.

Run: Get-Content .\targets\Calibre-Web-NextGen\docker-compose.yml

We'll review the configuration first and make sure its persistent test data won't accidentally go somewhere we don't want.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/74e108d580328e42a604d6e4b4c112eab1929e50/Screenshot%202026-09-18%20233929.png)

This tells us exactly what we need before launching it.

The Compose file uses the official NextGen image, exposes 8083, and requires four host-side volume paths. We should not run this file unchanged, because /path/to/... are placeholders. For Lab 5, we'll create isolated test directories on your external D: drive so we don't mix research data with your real files.

**Create isolated test-data folders**

Run this one command: 

You should end up with: New-Item -ItemType Directory -Force -Path "D:\Vulnerability-Research-Docker\calibre-lab5\config","D:\Vulnerability-Research-Docker\calibre-lab5\ingest","D:\Vulnerability-Research-Docker\calibre-lab5\library","D:\Vulnerability-Research-Docker\calibre-lab5\plugins"

D:\Vulnerability-Research-Docker\calibre-lab5\

├── config

├── ingest

├── library

└── plugins

These will be Lab 5-only test data, which is exactly what we want for controlled dynamic testing and later restoration. After, Then we'll create a separate Lab 5 Compose file rather than modifying the target repository's original docker-compose.yml.

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/9d39a795375e89ea27465b07f7917ee7e484012a/Screenshot%202026-09-18%20234342.png)

This confirms all four isolated Lab 5 directories were created successfully on the D: external drive:

config, ingest, library, and plugins.

**create our Lab 5 Compose file**

We won't modify the repository's original Compose file. From your current PowerShell location, run: Copy-Item .\targets\Calibre-Web-NextGen\docker-compose.yml .\docker-compose-lab5.yml

Then verify it exists: Get-Item .\docker-compose-lab5.yml | Select-Object Name,Length

After that, we'll edit only our copy to point those four volumes to D:\Vulnerability-Research-Docker\calibre-lab5\..., while preserving the target's original configuration for comparison and evidence.



This confirms our separate docker-compose-lab5.yml was created successfully at 3,507 bytes. The original target Compose file remains untouched.

Now we'll modify only the four placeholder volume paths in our Lab 5 copy.

**Replace the volume paths**

Run this PowerShell block: $compose = Get-Content .\docker-compose-lab5.yml -Raw
$compose = $compose.Replace('/path/to/config/folder','D:/Vulnerability-Research-Docker/calibre-lab5/config')
$compose = $compose.Replace('/path/to/the/folder/you/want/to/use/for/book/ingest','D:/Vulnerability-Research-Docker/calibre-lab5/ingest')
$compose = $compose.Replace('/path/to/your/calibre/library','D:/Vulnerability-Research-Docker/calibre-lab5/library')
$compose = $compose.Replace('/path/to/your/calibre/plugins/folder','D:/Vulnerability-Research-Docker/calibre-lab5/plugins')
Set-Content .\docker-compose-lab5.yml $compose

No output is normal.

Then verify only the volume section: Select-String -Path .\docker-compose-lab5.yml -Pattern "D:/Vulnerability"

We still won't start the container yet. I want to validate the resulting Compose configuration first so we don't discover a path/YAML problem after downloading the image.



The replacement worked. Your screenshot confirms all four Lab 5 bind mounts now point to the external D: drive:

D:/Vulnerability-Research-Docker/calibre-lab5/config:/config

D:/Vulnerability-Research-Docker/calibre-lab5/ingest:/cwa-book-ingest

D:/Vulnerability-Research-Docker/calibre-lab5/library:/calibre-library

D:/Vulnerability-Research-Docker/calibre-lab5/plugins:/config/.config/calibre/plugins

One extra match is just a comment mentioning the config path, so that's fine.

Next step — validate the Compose file

Before downloading or starting anything, run: docker compose -f .\docker-compose-lab5.yml config --quiet

If the configuration is valid, it will normally return to the prompt with no output. Once this passes, we'll be ready for the first controlled startup of Calibre-Web NextGen for Lab 5.



No output and a return to the PowerShell prompt means the Compose configuration passed validation. ✅

We're ready for the first controlled startup of Calibre-Web NextGen.

**First container startup**

Run: docker compose -f .\docker-compose-lab5.yml up -d

This will pull the Calibre-Web NextGen image and start the local container. The first run may take several minutes. We won't open localhost:8083 until we confirm the container is actually healthy.



This is a successful first startup.

27/27 image layers pulled
ghcr.io/new-usemame/calibre-web-nextgen:latest — Pulled
Docker network — Created
calibre-web-nextgen — Started

So the container is running, but we still need to verify its actual state before opening the application.

**Verify container status**

Run: docker compose -f .\docker-compose-lab5.yml ps

We're looking for calibre-web-nextgen showing Up and port 8083 mapped correctly. After that, we'll check its startup logs before opening the web interface.



This confirms the container is running correctly:

Status: Up 4 minutes (healthy)
Port: 8083 → 8083/tcp
Container: calibre-web-nextgen

That (healthy) status is exactly what we wanted. 

Before opening the application, let's do one final startup-log check for errors.

Run: docker compose -f .\docker-compose-lab5.yml logs --tail=30

We'll open Calibre-Web NextGen at localhost:8083 and establish our Lab 5 baseline.



The startup logs look clean. The important checks all passed:

Container is healthy.
Gevent server started on port 8083.

The localhost connection test succeeded.

/cwa-book-ingest and /calibre-library were recognized.

Docker Desktop correctly switched file watching to polling.

Database schema initialized successfully.

Checksum/backfill completed successfully.

No startup exception or fatal error appears in the log you provided.

One thing to note for later: the log says HARDCOVER_TOKEN is configured because the stock Compose file contains the placeholder your_hardcover_api_key_here. That's not blocking us, but we'll clean that up later if necessary.

docker compose -f .\docker-compose-lab5.yml ps


**Open the application**

Now open your browser and go to:

http://localhost:8083

This stays entirely on your local machine.



This is the baseline we wanted. The application is reachable locally at localhost:8083, and it redirects unauthenticated access to /login?next=%2F. The login page renders normally.

**Prepare Dynamic Testing Environment**

The Docker portion is now working.

Before trying credentials, we need to determine the application's intended initial/default administrator login procedure rather than guessing.

From PowerShell in C:\Users\eelve\Vulnerability-Research-Lab, run: docker compose -f .\docker-compose-lab5.yml logs | Select-String -Pattern "admin|password|username|login|credential" -CaseSensitive:$false

We'll use that to determine whether the fresh installation generated credentials or expects a documented default. Don't enter random credentials yet.



That confirms the logs do not expose or generate initial credentials. The two matches are only authentication-related configuration messages:

SESSION_COOKIE_SECURE set to False (Standard/LDAP login)
Magic Link QRCode is enabled

So we shouldn't guess credentials.

find the documented/default login in the source

Run: Get-ChildItem .\targets\Calibre-Web-NextGen -Recurse -File -Include *.md,*.py,*.yml,*.yaml | Select-String -Pattern "admin123|default password|default username|initial password|initial login" -CaseSensitive:$false | Select-Object -First 20 Path,LineNumber,Line

This keeps us within the authorized local source repository and will tell us how a fresh installation is intended to be accessed.



We found the intended fresh-install credentials directly in the project's source:

Username: admin

Password: admin123

The strongest evidence is cps\constants.py, which defines DEFAULT_PASSWORD = "admin123", plus cps\ub.py, which documents creation of the default admin account. The repository documentation also explicitly states admin/admin123.

**Initial login**

On the login page, enter:

Username: admin

Password: admin123

This is the application's documented default account in our isolated local instance—not a credential-guessing test. Don't change the password, create users, or modify settings yet. We want to capture the clean authenticated baseline first.



This confirms the documented default administrator login succeeded and the authenticated application is functioning normally.

Your screenshot also establishes a useful Lab 5 baseline: authenticated admin session, Books (0), empty library, and the classic Calibre-Web NextGen interface. Save this screenshot as evidence for Lab 5 / Step 1 — Initial Authenticated Baseline.

We now need controlled test data before Candidate #1 can be tested. The read-status authorization candidate requires at least one book object, and later we'll need separate users with different visibility/access conditions.

**Verify the library is empty on disk

Don't change anything in the web interface yet. In PowerShell run: Get-ChildItem "D:\Vulnerability-Research-Docker\calibre-lab5\library" -Force

No output is expected if our clean library directory is still empty.

After that we'll add a harmless test book and then create the controlled accounts needed for Candidate #1.



This is actually the expected clean-library state.

The directory isn't literally empty because Calibre-Web NextGen initialized its database files:

metadata.db

metadata.db-shm

metadata.db-wal

But there are no book files/directories yet, which matches the web interface showing Books (0). So we have a clean baseline. 

Create harmless test book #1

Rather than use a real copyrighted book, we'll create our own simple .txt test book. Run: Set-Content "D:\Vulnerability-Research-Docker\calibre-lab5\ingest\Lab5-Test-Book-01.txt" "Lab 5 authorized vulnerability research test book. No real user data."

After that, refresh the Calibre-Web page.

We're looking for the book count to change from Books (0) to Books (1). If it doesn't, we'll check the ingest logs rather than guessing.


The screenshot confirms the book was not ingested—the interface still shows Books (0). That's useful evidence; we won't change anything blindly.

The most likely next place to look is the ingest service logs.

Check what happened to the test file

Run: docker compose -f .\docker-compose-lab5.yml logs --since=5m | Select-String -Pattern "Lab5|ingest|txt|error|unsupported|failed" -CaseSensitive:$false



This tells us the ingest did work, but it took about 73 seconds to convert the TXT to EPUB. The important lines are:

New file detected → Converting file from txt to epub → EPUB output written → Conversion ... complete

The repeated Fontconfig messages are warnings about its cache directories; they did not prevent the conversion.

Refresh http://localhost:8083 again now.

Don't add another book yet.

We're checking whether Books (0) has now changed to Books (1).



That confirms the complete ingest path is working. Your screenshot shows Books (1) and Lab5-Test-Book-01 is visible in the local library. This is good Lab 5 baseline evidence.

We now have the application running, authenticated admin access, an isolated database/library, and one controlled test object. Next we need a non-admin test user for Candidate #1.

Open User Management

In Calibre-Web NextGen, click the wrench/tools icon 🔧 near the top-right.

Look for Admin, User Management, Edit Users, or a similar user-management option.
