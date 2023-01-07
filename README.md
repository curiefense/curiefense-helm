# curiefense-helm
Helm charts for the Curiefense project

## Update instructions
### Update to newer upstream istio version
* Update the `curieproxy-istio` in <https://github.com/curiefense/curiefense>, submit it as a PR if the test at the end of this section succeds. Build it locally.
* Download the istio release from <https://github.com/istio/istio/releases/tag/VERSION>
* Extract it
* Use a diffing tool (ex. meld) to compare the `istio-<VERSION>/manifests/` folder of the newer release, and this repository's `istio-helm` folder
* Make both folders as similar as possible, keeping only curiefense-specific contents. For example:
  * `waf` value in `charts/gateways/istio-egress/values.yaml` and `charts/gateways/istio-ingress/values.yaml`
  * memory limit bump to 2048 Mi in `charts/gateways/istio-ingress/values.yaml`
  * `externalTrafficPolicy` in `gateways/istio-ingress/values.yaml`
  * curiefense-related values in the `proxy:` section of `gateways/istio-ingress/values.yaml`
  * EnvoyFilters `curiefense_access_logs_filter` and `curiefense_lua_filter` in `charts/gateways/istio-ingress/templates/`
  * curiefense-related settings in `charts/gateways/istio-ingress/templates/deployment.yaml`
* Update `istio-helm/README.md`
* Do a local minikube deployment following <https://docs.curiefense.io/installation/deployment-first-steps/istio-via-helm>, replacing the image of the ingress proxy with the one built in the first step of this section
* Update the `deploy/istio-helm/charts` folder of <https://github.com/curiefense/curiefense> so that its contents is the same as this repo's `istio-helm/charts/` folder
* Open PRs to both <https://github.com/curiefense/curiefense> (image update, deploy/istio-helm/) and <https://github.com/curiefense/curiefense> (charts update)
* Prepare Helm packaging following the instructions below, do the release once the first 2 PRs have been merged.

### Helm packaging
* Choose a new chart version identifier (1.5.4 here)
* Update the value of `appVersion` in `curiefense-helm/curiefense/Chart.yaml`
* Commit, push, tag commit, push the tag
```
git tag -a curiefense-1.5.4 -m "Bump curiefense chart to version 1.5.4"
git push origin curiefense-1.5.4
```
* Generate helm package
```
helm package curiefense-helm/curiefense --app-version 1.5.0 --version 1.5.4
```

### Release on github
* Go to <https://github.com/curiefense/curiefense-helm/releases>
* Click `Draft a new release`
* Choose the tag that has just been created (`curiefense-1.5.4`)
* Set 'Release title' to `curiefense-1.5.4`
* Document changes to the chart in the releases notes field
* Attach `curiefense-1.5.4.tgz`
* Click "Publish release"

### Update the charts index
```
git checkout gh-pages
helm repo index . --url https://github.com/curiefense/curiefense-helm/releases/download/curiefense-1.5.4/ --merge index.yaml
```
* Only add new lines for the new release and generated line at the bottom of the file, not URL or timestamp changes for previous releases
```
git add -p
```
* Review changes
```
git diff --cached
```
* If it looks good, commit then throw away unwanted changes, then push
```
git commit -sm "Update index for release 1.5.4"
git reset --hard HEAD
git push origin gh-pages
```
### Check
```
helm repo add curiefense https://helm.curiefense.io/
helm repo update
helm search repo curiefense
```

Expected output:
```
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
curiefense/curiefense   1.5.4           1.5.0           Complete curiefense deployment
```

