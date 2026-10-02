# Voxgig SDK Generator DX Report

## Setup

- OS: Windows 11
- Node.js: v24.21.0
- npm: 11.19.0
- Git: 2.55.0.windows.5
- API: Resend
- OpenAPI spec: official `resend.yaml`
- SDK target: TypeScript
- Human work time: ~30 minutes

## What I tried

I used the Voxgig SDK tooling to generate a TypeScript SDK for Resend from its official OpenAPI specification.

The goal was to generate the SDK and then test it against the real Resend API using an API key.

## What worked

The Resend OpenAPI spec was successfully parsed after running `voxgig-apidef` manually.

It generated the API information correctly, including:

- API name: Resend
- Server: `https://api.resend.com`
- Authentication: Bearer token through the `Authorization` header
- API entities such as Email, Domain, Contact, Usage, Webhook and others

Around 60 entity model files were generated.

## Issues I hit

### 1. Project name was required

Running:

`npm create @voxgig/sdkgen`

failed with:

`The first argument must be the project name.`

It was not immediately clear from the initial command that a project name was required.

### 2. Automatic npm install failed on Windows

The scaffold reached:

`running npm install`

but failed with:

`Failed to start npm: spawn npm ENOENT`

`npm` worked normally when I ran it directly from the same terminal.

### 3. `--no-install` recovery was not obvious

I tried `--no-install`, but using it together with `--target ts` failed because the target required the project toolchain to already be installed.

The error explained what was wrong, but the recovery flow was not obvious.

### 4. OpenAPI model had to be generated manually

After recovering the scaffold, `api-info.aontu` and the entity model were initially empty.

Running:

`npx voxgig-apidef resend --folder . --def .\def\resend.yaml --prefix ""`

successfully populated the semantic model.

### 5. `npm run generate` had a path resolution issue

`npm run generate` could not find:

`api/api-info.aontu`

even though the file existed under:

`model/api/api-info.aontu`

The error output also showed a mix of Windows paths such as:

`C:\Users\ADMIN\...`

and Unix-style paths such as:

`/Users/ADMIN/...`

Running `voxgig-model` directly from inside the `model` directory got past this issue.

### 6. TypeScript SDK generation then failed on a missing template path

The generator successfully started creating TypeScript entities, but stopped with:

`ENOENT: no such file or directory`

for:

`model\tm\ts\src\feature\test`

At this point I stopped because I had reached the 30-minute timebox.

## Result

The Resend OpenAPI specification was successfully converted into Voxgig's semantic model, but the final TypeScript SDK generation did not complete because of the missing template path.

I therefore did not reach the live API-key test.

## Main DX feedback

The error messages were detailed and useful for debugging, but the Windows setup/recovery path could be clearer.

In particular, clearer guidance around automatic npm install failures, `--no-install`, manually running `apidef`, and Windows path handling would make it easier to recover without needing to inspect the generated project structure.