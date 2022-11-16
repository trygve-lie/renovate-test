# Eik Shared Libs

This project is used to maintain up to date ESM versions of key shared libraries used at Finn.
These are libraries whos versions we want to align teams on across the organisation in order to reduce their rebundling in applications.
See the packages folder for the current complete list of libraries.

## How it works

Renovate bot is employed to update each library whenever new versions become available. PRs from Renovate are auto merged which kicks off a new build and publish for that library.
After this, each new version will be available on Eik and after some time has passed (usually 1 week) we move the Eik alias for the library to the new version. This last part is 
intentionally a manual process to help ensure bad builds don't break production apps.

## Special Cases

* React does not support ESM yet so we are forced to maintain an ESM build script for React
* React DOM contains a reference to React (import react from "react") and we map this to an Eik version of React
* Lit does not provide a "full" package export (even though they provide one on CDN) so we are forced to maintain a build script that includes the entirity of Lit packages in one

## Development

### Install

Running `npm install` from the repo root will install all dependencies for all workspaces.

### Scripts

Build and copy scripts are maintained in the scripts folder. These are referenced and used for each of the workspaces.

### Copy

Each workspace includes a copy script in package.json scripts though for many, this is just a noop. The copy script is used for repos that don't need to build anything and can 
simply copy lib files out of node_modules and place them in a dist folder for publishing to Eik.

### Build

Each workspace includes a build script in package.json scripts though for many, this is just a noop. The build script is used to run esbuild or rollup to produce an Eik friendly version. Libraries like Lit and React need to run builds since they do not provide a build in the state we need for publishing to Eik.

### Publish

Each workspace includes an eik:publish:ci run script in package.json scripts. This script will log into the Eik server and publish everything defined in the the package.json "eik" section for the workspace. This normally means that once copy and build are complete, a dist folder will contain files that the eik:publish:ci command will then upload.

## CI

Refer to the .github/workflows/publish.yml file for CI instructions. In short though, on CI the following commands are run:

* Install: `npm install`
* Build: `npm run build`
* Copy: `npm run copy`
* Publish: `npm run publish`