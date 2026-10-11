# Lab 5 — Dynamic Testing: Object-Level Authorization in Calibre-Web NextGen

> **Status:** In progress · Authorized local research only · No confirmed vulnerability or assigned CVE

## Project overview

In this lab, I am taking a potential object-level authorization issue from static analysis into a running application. My goal is to determine whether a non-administrator account can change the reading status of a book it is not allowed to see. I am testing only my own local instance, using synthetic books and accounts that I created for this research.

The core question is straightforward: **Does the server enforce book visibility rules when it receives a direct read-status request, or does it rely on the user interface to hide restricted books?**

## Research baseline

| Item | Baseline |
|---|---|
| Application | Calibre-Web NextGen |
| Source version | v4.1.43 |
| Git commit | `7e9221f455329bb3e6611b4652ac23b7f8629bb0` |
| Source branch | `main` |
| Test scope | Local Docker instance; synthetic content; controlled user accounts |
| Candidate | Read-status object authorization (`/ajax/toggleread/<book_id>`) |

## Walkthrough — what I did and why

### 1. Source-code baseline and reproducibility

I started by verifying that my local source checkout matched the code reviewed during static analysis. I checked for uncommitted changes, recorded the Git commit and branch, and read the repository VERSION file. This matters because security findings are only reproducible when the tested code is identified precisely.

**Commands / configuration I used**

```text
`git -C .\targets\Calibre-Web-NextGen status --short`
`git -C .\targets\Calibre-Web-NextGen rev-parse HEAD`
`git -C .\targets\Calibre-Web-NextGen branch --show-current`
`Get-Content .\targets\Calibre-Web-NextGen\VERSION`
```

**What I observed**

The checkout was reported clean on `main`, at commit `7e9221f455329bb3e6611b4652ac23b7f8629bb0`, with source version `4.1.43`. Searching `cps/constants.py` for `STABLE_VERSION` and recursively searching `cps` for the literal version did not locate it; the top-level VERSION file did.

<details>
<summary>Original screenshots for this stage (7)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/97bf0a3d69e8a973cc74209b3e5e690070ef3215/Screenshot%202026-09-17%20092356.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/35a979ca0bc7bb66ea31c9a98eb464a94947fc68/Screenshot%202026-09-17%20092345.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/27a78aebd5813135ec1488ff1384d945ff61fbf0/Screenshot%202026-09-17%20092826.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a4d3fa4f575f4a5548816fe36d0e89af1cba468e/Screenshot%202026-09-17%20093417.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5d345134ab50ded4085c0ab361ac7eec81d9f393/Screenshot%202026-09-17%20093645.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/20b43b8dcd4051b7a584459a51609ba2d8a28745/Screenshot%202026-09-17%20093828.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/48627edfb05f26d35360a211477d4d0f86132331/Screenshot%202026-09-17%20094042.png)

</details>

### 2. Python runtime investigation

I first explored whether I could launch the target directly in my existing Python virtual environment. This gave me a clearer picture of the application’s dependencies before I chose a runtime.

**Commands / configuration I used**

```text
`.\.venv\Scripts\Activate.ps1`
`python .\targets\Calibre-Web-NextGen\cps.py --help`
`python -m pip install -e .\targets\Calibre-Web-NextGen`
```

**What I observed**

The direct launch failed because Flask was missing. The editable installation encountered native-package build problems, including `python-ldap` requiring Microsoft Visual C++ 14.0 or later. Source inspection showed LDAP imports are handled as optional at runtime, but the overall dependency set remained difficult to reproduce on Windows. A separately installed `faust-cchardet` version did not match the target’s declared version constraint. I did not treat this failed bare-metal setup as an application vulnerability.

<details>
<summary>Original screenshots for this stage (20)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b917224140e49cc5985e38284c0e7efc2fa45c6f/Screenshot%202026-09-17%20094417.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/028f974aee69bccf74d43e0ffd2a2d0bed1e65b6/Screenshot%202026-09-17%20094721.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4fb21783450b1402ff962932d9327a1433a780ad/Screenshot%202026-09-17%20094933.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8873d733a065ab660fad60294b6bb545e6721da/Screenshot%202026-09-17%20095155.png)

![Inage alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/18665aca2ed3554c78e7b1ba219d8d4e0340f4c1/Screenshot%202026-09-17%20100759.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d9c9cc19bf1268c7b2cddd4b7f8624dc17f4cb4d/Screenshot%202026-09-17%20101146.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4a822239a08e8f6256507fa1ae0970a78d57b739/Screenshot%202026-09-17%20101203.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a1cef7e4bb4eb2c1ac5babd273801aafe89b70a4/Screenshot%202026-09-17%20101407.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/8a445e77ebe82afdaa1d93b9711ce053f4f12922/Screenshot%202026-09-17%20103709.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/a6cfe80198240bcb84a42a583f1eda6200b33700/Screenshot%202026-09-17%20103937.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/51a05c4c82922567a1d9482f59c31343800ccda4/Screenshot%202026-09-17%20104140.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c214a14336f548eb66b5a96deb415e0edb9d742/Screenshot%202026-09-17%20104558.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/90f13224abf8ee5546af2f89d11791704dced89d/Screenshot%202026-09-17%20104752.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b8dc5a01f3a5ac979fdcacdb80a305d67e7da484/Screenshot%202026-09-17%20104932.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ae7ec2996c3933caf7dffb5378e142373611d76a/Screenshot%202026-09-17%20105315.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/34ed439b886db0c4d363171567d6f1c064537472/Screenshot%202026-09-17%20110110.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ff5fe89255c860e0b2e16236c3a2825ebf69ce46/Screenshot%202026-09-17%20110322.png)

![iamge alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/adbbc4b561287613263653157a10c216d694d2e8/Screenshot%202026-09-17%20110510.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/251855a2701af756853d92da4518c65c1327b673/Screenshot%202026-09-17%20110708.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1965acfd6e3ad7ebb00e93446b759faa9b340745/Screenshot%202026-09-17%20110926.png)

</details>

### 3. Choosing the supported container runtime

I checked the project’s own setup documentation and selected Docker Desktop with WSL2 for Windows. This was a way to run the application closer to its documented environment without modifying the target source code or forcing incompatible Python dependencies.

**Commands / configuration I used**

```text
`wsl --status`
`wsl --list --verbose`
`docker info --format "Docker Root Dir: {{.DockerRootDir}} | Server Version: {{.ServerVersion}}"`
```

**What I observed**

WSL2 was available; Ubuntu was installed, followed by Docker Desktop. I initially configured Docker storage on an external D: drive. That original storage choice is part of the historical setup and was later changed during recovery.

<details>
<summary>Original screenshots for this stage (13)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d74e1eae4bf30e8f19eaab56ecc8d157aaf587fa/Screenshot%202026-09-17%20111128.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0aeaa48334f76f493a7167d4ff7f76613086671d/Screenshot%202026-09-17%20111432.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/81941459986962842b87fee27b01d0ff0612825b/Screenshot%202026-09-17%20111635.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0c9bf4917da5bb340d50c72bba1d921a56bec6a6/Screenshot%202026-09-17%20112003.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/08ee2fbce534724ce63eff24d4ff9151a9ea72b9/Screenshot%202026-09-17%20210557.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d495d55eaccf280dfac3b7cd4d9085d851ac8929/Screenshot%202026-09-17%20210740.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/833af0bd1d4195da937212a925d7cb4f6291fc75/Screenshot%202026-09-17%20210908.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5671c61cdf9112088b7621545ffa3a80c3854ad6/Screenshot%202026-09-18%20184257.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6c6a94fb37d1678cae6697e1021809f3717264f5/Screenshot%202026-09-18%20190940.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6cf3a4507f72169ef3ee949e2501fcc2488ac0d9/Screenshot%202026-09-18%20220643.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f05ad7084496e96abaf5f02413db0884b3394a6a/Screenshot%202026-09-18%20222320.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/418f6be8a966d2aa389995acb5f2a6c21bb8f7cf/Screenshot%202026-09-18%20222750.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/19982429774520746b6ec7fc7404586efdcc8042/Screenshot%202026-09-18%20223447.png)

</details>

### 4. Creating an isolated Lab 5 deployment

I inspected the repository’s Compose file and made a separate Lab 5 copy rather than editing the upstream file. I checked volume paths, validated the Compose configuration, and started the application. Keeping the lab isolated helps protect the original source checkout and separates test data from personal files.

**Commands / configuration I used**

```text
`Copy-Item .\targets\Calibre-Web-NextGen\docker-compose.yml .\docker-compose-lab5.yml`
`docker compose -f .\docker-compose-lab5.yml config --quiet`
`docker compose -f .\docker-compose-lab5.yml up -d`
`docker compose -f .\docker-compose-lab5.yml ps`
```

**What I observed**

The original deployment pulled the `latest` image and started on host port `8083`. Docker reported the container as healthy. The original bind-mounted test directories were on D:, and that arrangement was superseded during recovery. The `latest` tag alone does not prove which version was running at that time.

<details>
<summary>Original screenshots for this stage (9)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/15b8b1151bf07480f996ed778eb3d5770a3b68f8/Screenshot%202026-09-18%20223748.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/74e108d580328e42a604d6e4b4c112eab1929e50/Screenshot%202026-09-18%20233929.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/9d39a795375e89ea27465b07f7917ee7e484012a/Screenshot%202026-09-18%20234342.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/44c9f34633fe9a218df29da953f10dccd468c518/Screenshot%202026-09-18%20234745.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/277d689175a8f8b8bd190e8fede65bd7ba69eff3/Screenshot%202026-09-18%20235040.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6b6f2f219170b48d2779de4b67deb51304d44534/Screenshot%202026-09-18%20235559.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/aef2c58e63a6916759b028757fd1b2b94dca042c/Screenshot%202026-09-19%20001437.png)

![image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ce4e4b7ae38ae119fc22f1248b92a39ccbeddca5/Screenshot%202026-09-19%20001750.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7b3d7afb3cfafb3b87529a0cce78db1e154ed5f3/Screenshot%202026-09-19%20002112.png)

</details>

### 5. Initial application baseline

I opened the local web interface, located the documented default administrator credentials in the project source, and confirmed I could reach the authenticated library. I also checked the on-disk library state before importing any test material.

**Commands / configuration I used**

```text
`http://localhost:8083` (original deployment)
`docker compose -f .\docker-compose-lab5.yml logs`
```

**What I observed**

The application loaded successfully and initially showed zero books. The library directory contained Calibre metadata database files but no imported book objects. Default credentials are not included in this public write-up as reusable secrets.

<details>
<summary>Original screenshots for this stage (5)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b75138a3d7fcc8f2c3df7ae9ce88155bb2bc1b2a/Screenshot_19-9-2026_105658_chatgpt.com.jpeg)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/014bc3660a66eb71320759ed9e494f4846a252e5/Screenshot%202026-09-19%20003135.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1e4d5c085dd10daf8d88556cd765e184f9cb1b33/Screenshot%202026-09-19%20003439.png)

![Image alt](Screenshot_19-9-2026_04441_localhost.jpeg)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/3479ba64817e766faa41ed0d9272bfdf5d1ad9c9/Screenshot%202026-09-19%20110903.png)

</details>

### 6. Building the controlled authorization dataset

I created two synthetic, non-copyrighted test books and a non-administrator user. I kept one book accessible and applied a deny-tag rule to the second book. This produced a clear authorization boundary for testing whether a direct object-ID request could reach an object hidden from the user.

**Commands / configuration I used**

```text
Original ingest method: write synthetic TXT files to the dedicated ingest directory; the application converts them to EPUB.
Test account: `lab5-user-a` (non-admin; eBook viewer permission only).
Deny tag: `lab5-restricted`.
```

**What I observed**

The original environment displayed two books. In that environment, the permitted book was ID `2`, and the restricted book was ID `3`. The second book had the `lab5-restricted` tag, and the test user had a matching deny-tag restriction. These IDs were specific to the original database and **must be rechecked** after recovery.

<details>
<summary>Original screenshots for this stage (15)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f2865bc249a59627ab1cc539bd4ea1e2935499f9/Screenshot%202026-09-19%20012052.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b113c2d535a9a9ce9f51aeb5ba425914a04a88ab/Screenshot%202026-09-19%20112203.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/54ef835a347727a2ccce7c67dd11a3a98622e600/Screenshot%202026-09-19%20012720.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/128d3f49f65122f0a908f4ef71d51f6ecef0b201/Screenshot%202026-09-19%20120155.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/3e4b7826a1539cdc363210144db84a9babbf80be/Screenshot%202026-09-19%20120401.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/37e6f77e865028b1c283cecba7adabe43312e260/Screenshot%202026-09-19%20120619.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/98172fbee65c32c1587e133059c0194dcf3b7b88/Screenshot%202026-09-19%20120744.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/c70731f3d8b5bf51c406cfece9da955cda585999/Screenshot%202026-09-19%20125022.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/e057cb0edbb829026d2d0b9a08faf98c9c3dfb61/Screenshot%202026-09-19%20125704.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/36b245b33ed530724ec156b3d2405c0842d93885/Screenshot%202026-09-19%20131342.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/40513bbff67ce88c966c124f7ecd6753ace437ea/Screenshot%202026-09-19%20132519.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/878c757a9204b13c908ac61d1eb0c7c053c2e31f/Screenshot_19-9-2026_132814_localhost%20Settings.jpeg)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f5116f83b0700dfa7a5bf8e4f5ad2cac638bd2c9/Screenshot%202026-09-19%20133735.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f366986c0c6ea53cf9f713e2e6405c64f39aefb8/Screenshot%202026-09-19%20133807.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ec5e4439b1c19175106634a62a5d48bf16818451/Screenshot%202026-09-19%20134043.png)

</details>

### 7. Confirming the normal visibility boundary

I signed out of the administrator account and logged in as the low-privilege test user. Before manipulating any requests, I checked what that user could see through the normal application interface. This was essential control evidence: without a confirmed visibility restriction, a later direct request would not demonstrate an authorization boundary.

**Commands / configuration I used**

```text
Open the Books page as the restricted test user; compare with the administrator’s view.
```

**What I observed**

The original restricted account showed only the permitted book. The denied book was absent from its normal library view. This establishes UI visibility behavior; it does not, by itself, prove that every server endpoint enforces the same rule.

<details>
<summary>Original screenshots for this stage (2)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2c7399779be115b717cf9e85a2c78adb596d163c/Screenshot%202026-09-19%20134359.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2629f36d3d5fb8c3c313dd677616379e657bcc52/Screenshot_19-9-2026_13467_localhost%20second%20book%20invisable.jpeg)

</details>

### 8. Capturing the authorized read-status request

I opened the permitted book and used Microsoft Edge DevTools to record the application’s normal read-status action. I inspected the Network entry to identify the method, endpoint, response, and form data that a valid request requires.

**Commands / configuration I used**

```text
`POST /ajax/toggleread/2` (original database)
Original local origin: `http://localhost:8083`
```

**What I observed**

The original control request returned HTTP `200 OK`. Its request included a CSRF token and XHR-related headers. This is the reference request for a controlled one-variable comparison.

<details>
<summary>Original screenshots for this stage (6)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/762bb74c4fcf96c3ecb409b924e407fd075faa3d/Screenshot%202026-09-21%20122158.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2a3d7b0c76bde798683302a8a48f7c3e5c96878b/Screenshot%202026-09-21%20125540.png))

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ec5a64d2ab1fc05b110a7f3db7ec7005c8b18a58/Screenshot%202026-09-21%20122748.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/89701581acc649431d2993d3868691c281aa30ba/Screenshot%202026-09-21%20122831.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/49c8b961284896c5304fdfb9fc478821c9aa1147/screenshot.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/6d555c9f4a7382ff04b33f1b1e01917e9315190e/Screenshot%202026-09-26%20213259.png)

</details>

### 9. Attempting the restricted-object request

I tried to reproduce the authorized action against the restricted book by changing the object ID while retaining the same user session. My first attempt used Edge’s Edit and Resend feature, but it failed at the browser tooling layer. I then tried a browser-console fetch request to determine whether the server could be reached.

**Commands / configuration I used**

```text
Original intended comparison: `POST /ajax/toggleread/2` → `POST /ajax/toggleread/3`
```

**What I observed**

The Edit and Resend attempt showed `FETCH_ERROR`, so it was not valid evidence of a server-side authorization decision. A later bare `fetch` reached the application but returned HTTP `400`; server logs identified a missing CSRF token. This 400 was a request-validity failure, not evidence that object-level authorization succeeded.

<details>
<summary>Original screenshots for this stage (7)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/b7d4f005007c6e02426f92f2cff0147811dd1bfd/Screenshot%202026-09-26%20220251.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/e888346cb4aa397635d7a6e39ec477acfad4dcfe/Screenshot%202026-09-26%20220817.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7791711e8089e56e036f454d3820784fcf4795a2/Screenshot%202026-09-26%20222554.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/7529fe664d75940fb869236ae68badd4b8bb550a/Screenshot%202026-09-26%20221251.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/0b93e8a6b0395d17956ceca92dfa686a09bce5c8/Screenshot%202026-09-26%20223821.png)

![image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/883a7134efb229f026ece08aa42681fcc8072f97/Screenshot%202026-09-26%20224343.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ee05ef5bfa0ad9bc29a6cc53488429e1d1572374/Screenshot%202026-09-26%20224655.png)

</details>

### 10. Troubleshooting request equivalence and CSRF

I inspected the legitimate request payload and headers so that a replay would reproduce the normal browser request as closely as possible. I investigated CSRF form data, token freshness, `X-CSRFToken`, `X-Requested-With`, and `Content-Type` rather than treating every HTTP 400 as a security finding.

**Commands / configuration I used**

```text
Observed legitimate content type: `application/x-www-form-urlencoded; charset=UTF-8`
Observed XHR header: `X-Requested-With: XMLHttpRequest`
Observed endpoint pattern: `/ajax/toggleread/<book_id>`
```

**What I observed**

Several manipulated requests returned HTTP `400`, including attempts with stale or incorrectly constructed CSRF data. A final closer-format request was prepared, but the provided documentation does not establish a verified server-side change to the restricted book. **Candidate #1 remains unconfirmed.**

<details>
<summary>Original screenshots for this stage (20)</summary>

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/457fa3c85a237dad6676bdf3866438d9379eb628/Screenshot%202026-09-26%20225326.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/f3c89eca377a23c454b2ed35d3f694106f02b2e7/Screenshot%202026-09-26%20231639.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/d96128923ddf749e1896b064a71a82cd1d46e22f/Screenshot%202026-09-26%20231639.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/becb8688386307fc4a27b9fce31bde21c72bb76e/Screenshot%202026-09-26%20232744.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/ee6b18404b1ffe4f3b469b5019dec3dbfb46a0e3/Screenshot%202026-09-27%20001246.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/125f8a9535692410ab07f0744f57ede5b2e5bb8f/Screenshot%202026-09-27%20114846.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/652890e69a832c42ee310c356bdba34b64ebea4c/Screenshot%202026-09-27%20114338.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/985b8d123ce9a39c9a34b950ac508e375d852526/Screenshot%202026-09-27%20003528.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/1c315d64ee56159a91ebce6723ca7e88c13798cc/Screenshot%202026-09-27%20004210.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/4703d524da2b289d5d9d2218a4898a7e22efad2b/Screenshot%202026-09-27%20122159.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/512dd4cf3586171349f05fb35bd1ef2ebe29b536/Screenshot%202026-09-27%20004826.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/28a5571a431af0cfa00e73d2e63da45e8ea3cb00/Screenshot%202026-09-27%20005000.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/829f1dc5f55c00500938d2dc3b7dea6ebc2e08b2/Screenshot%202026-09-27%20005245.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/db5f754730f8a3da0d259d562895ebbb289a4980/Screenshot%202026-09-27%20010848.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/fc7576133f4e6a47c1392f6a143904aa76ad1103/Screenshot%202026-09-27%20125018.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/83b9e490f229dee39f894435ec5004fd7d624687/Screenshot%202026-09-27%20125457.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/65b5244e2ba090f64e7bb2710813ef36e1a2932b/Screenshot%202026-09-27%20010848.png)

![mage alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/2f6412422d8df37321d546c613612da44db29b54/Screenshot%202026-09-27%20011204.png)

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/5d7528edea6a2b32bfc5e2929984ded24af7ec43/Screenshot%202026-09-27%20011400.png))

![Image alt](https://github.com/Kevinolee1/Dynamic-Testing/blob/791b312db92a9978d96e56f32c3894723185dbe1/Screenshot%202026-09-27%20155017.png)

</details>

## Environment recovery — October 2026

After the initial authorization testing, Docker storage failures interrupted the lab. I did not interpret these infrastructure failures as evidence about the target application. Instead, I recovered Docker Desktop and re-established a version-controlled testing environment.

The earlier deployment used storage on an external D: drive. For the rebuilt environment, I configured Docker Desktop’s WSL storage on the E: drive and preserved the previous data folders rather than deleting them. The recovery required correcting a stale Docker Desktop storage path, initializing a fresh Docker backend, and creating fresh Compose-managed volumes.

I also discovered that the newly pulled `latest` image was **v4.1.47**, while my Lab 4 source baseline was **v4.1.43**. I downloaded the `v4.1.43` image explicitly and updated the Lab 5 Compose file to pin that version. Before switching, I saved a backup of the newer configuration volume and configured a separate `lab5-config-4143` volume so the older application would not directly reuse a database initialized by v4.1.47.

I changed the host-side port mapping to `8600:8083` because the previous host port was unavailable. I validated the Compose configuration and recreated the container. The final Docker status showed the v4.1.43 container running as **healthy**. I then opened the application at `http://localhost:8600`, logged in as administrator, changed the default administrator password, and confirmed that the new library was empty.

**Recovery verification commands**

```powershell
docker compose -f .\docker-compose-lab5.yml config --quiet
docker compose -f .\docker-compose-lab5.yml up -d --force-recreate
docker ps --filter "name=calibre-web-nextgen"
```

**Verified recovery state**

| Property | Rebuilt lab |
|---|---|
| Docker image | `ghcr.io/new-usemame/calibre-web-nextgen:v4.1.43` |
| Local URL | `http://localhost:8600` |
| Container status | `healthy` |
| Dedicated configuration volume | `lab5-config-4143` |
| Library | Empty; test books need recreation |
| Administrator access | Verified; default password changed |

> **Evidence note:** The earlier screenshots above belong to the original deployment on port 8083. They should not be presented as screenshots of the rebuilt environment on port 8600. The rebuild has been verified through command output; new screenshots will be captured as testing resumes.

## Next phase — repeatable authorization test

I will recreate the two synthetic books and the restricted account in the rebuilt v4.1.43 environment. I will record the **new book IDs** rather than assuming they are still 2 and 3. Before sending any manipulated request, I will verify that the restricted user can see the allowed book and cannot see the denied book.

Then I will capture a fresh authorized read-status request, reproduce its required headers and CSRF form data, and change only the target book ID for the restricted-object test. Finally, I will verify the result independently through server logs and persistent application state.

| Test | Expected evidence |
|---|---|
| Normal visibility | Allowed book visible; denied book hidden |
| Authorized control | Read-status request succeeds for allowed book |
| Restricted-object request | Exact HTTP status and response, with valid request formatting |
| Independent verification | Confirm whether the denied book’s server-side reading status changed |

**Interpretation rule:** A 200 response alone is not proof of an authorization vulnerability, and a 400 response alone is not proof that authorization is enforced. I will report a finding only if the request is valid and the observed server-side behavior demonstrates an unauthorized effect.

## Conclusion so far

This project has taught me to separate three different kinds of problems: environment setup failures, request-construction failures, and actual application security failures. I established a legitimate read-status control and a restricted-book visibility boundary in the original environment, but the available evidence does not yet confirm an object-level authorization bypass. The next phase is a clean, reproducible retest on v4.1.43.

---

*All testing is performed against an isolated local installation with synthetic data. No real users, third-party systems, or production content are involved.*
