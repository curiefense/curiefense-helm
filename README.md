# curiefense-helm
Helm charts for the Curiefense project

## Update instructions
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

