## Development & Deploy

Install ytt, kbld, kapp beforehand (https://k14s.io).

```
./hack/build.sh # to build locally

# add `-v image_repo=docker.io/username/secretgen-controller` with your registry to ytt invocation inside
./hack/deploy.sh # to deploy

./hack/dev-deploy.sh # to deploy quickly for iterating

export SECRETGEN_E2E_NAMESPACE=secretgen-test
./hack/test-all.sh
```

Note there's two deploy scripts above. You may wish to use the (slower)
`deploy.sh` at the beginning and end of iterating on a feature, as it will run
gofmt, check your mods, and do a clean / hermetic build. However once you're in
the loop of iterating and re-deploying small changes, you will definitely prefer
the `dev-deploy.sh` script as it is much faster.

## Release

Release versions are scraped from git tags in the same style as the goreleaser
tool.

Tag the release - it's necessary to do this first because the release process uses the latest tag to record the version.
```
git tag "v1.2.3"
```

Push the tag to GitHub.
```
git push origin v1.2.3
```

After pushing the tag to GitHub, the release process will be carried out by a GitHub workflow. 
This workflow will:
* Build and push the secretgen-controller container image to ghcr.io
* Build and push the secretgen-controller-package-bundle container image to ghcr.io
* Generate the release YAML (i.e. `release.yml`)
* Generate the package YAML (i.e. `package.yml`)
* Generate the package metadata YAML (i.e. `package-metadata.yml`)
* Create a GitHub release draft
* Upload `release.yml`, `package.yml` and `package-metadata.yml` to the GitHub release

After the release process finishes successfully, you can view the newly drafted release via
the GitHub UI [here](https://github.com/vmware-tanzu/carvel-secretgen-controller/releases).

The newly published release will be available as a Draft (i.e. not available to the public).
Release notes can be autogenerated by GitHub, but make sure to call attention to any points
that are not immediately clear from the autogenerated notes. Make sure to always thank external
contributors for their additions to kapp-controller in the release notes.

Once the release notes are ready, clicking the `Publish release` button in the GitHub UI to
make the release available to users.

#### Alpha Releases
Similar to above but we have a separate `build-alpha-release.sh` script and
`alpha-releases` folder, and we don't make a github release.
Version names should be like `v1.2.3-alpha.1`

Copy the yaml from the deploy script into the repo:
cp tmp/release.yml alpha-releases/1.2.3.yml

### TODO

- regenerate if params changed
- certificate rotation?
- secret name is static -> does not trigger change
- kapp versioned certificate
