# ZAP Action API Scan

A GitHub Action for running the OWASP ZAP [API Scan](https://www.zaproxy.org/docs/docker/api-scan/) to perform
Dynamic Application Security Testing (DAST). 

The ZAP api scan action uses the api definition to scan before reporting the results. The alerts will be maintained as a 
GitHub issue in the corresponding repository.

**WARNING** this action will perform attacks on the target website.
You should only scan targets that you have permission to test.
You should also check with your hosting company and any other services such as CDNs that may be affected before running this action.
ZAP will also submit forms which could result in a [large number of messages](https://www.zaproxy.org/faq/how-can-i-prevent-zap-from-sending-me-1000s-of-emails-via-a-contact-us-form/) via, for example, 'Contact us' or 'comment' forms.

## Inputs

### `api_definition`

**Required** The file/URL where to find the definition of the api to be scanned. This can be either 
a publicly available endpoint or a local repository file.

### `format`

**Required** You need to specify the format which is used in the api definition. The available
options are "openapi", "soap" or "graphql".

### `docker_name`

**Optional** The name of the docker file to be executed. By default the action runs the stable version of ZAP. But you can 
configure the parameter to use the weekly builds.

### `cmd_options`

**Optional** Additional command lines options for the full scan script

### `issue_title`

**Optional** The title for the GitHub issue to be created.

### `token`

**Optional** ZAP action uses the default action token provided by GitHub to create and update the issue for the full scan.
You do not have to create a dedicated token. Make sure to use the GitHub's default action token when running the action(`secrets.GIT_TOKEN`).

### `fail_action`

**Optional** By default ZAP Docker container will fail with an [exit code](https://github.com/zaproxy/zaproxy/blob/efb404d38280dc9ecf8f88c9b0c658385861bdcf/docker/zap-full-scan.py#L31), 
if it identifies any alerts. Set this option to `true` if you want to fail the status of the GitHub Scan if ZAP identifies any alerts during the scan.


-- From this point on, this file is still in WIP stage
## Example usage

** Basic **
```
steps:
  - name: ZAP Scan
    uses: zaproxy/action-full-scan@v0.2.0
    with:
      target: 'https://www.zaproxy.org/'
```

** Advanced **

```
on: [push]

jobs:
  zap_scan:
    runs-on: ubuntu-latest
    name: Scan the webapplication
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: master
      - name: ZAP Scan
        uses: zaproxy/action-full-scan@v0.2.0
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          docker_name: 'owasp/zap2docker-stable'
          target: 'https://www.zaproxy.org/'
          rules_file_name: '.zap/rules.tsv'
          cmd_options: '-a'
```

## Localised Alert Details

ZAP is internationalised and alert information is available in many languages.

You can change the language used by this action by changing the locale via the `cmd_options` e.g.: `-z "-config view.locale=fr_FR"`

This is currently only available with the `owasp/zap2docker-weekly` or `owasp/zap2docker-live` Docker images.

See [https://github.com/zaproxy/zaproxy/tree/develop/zap/src/main/dist/lang](https://github.com/zaproxy/zaproxy/tree/develop/zap/src/main/dist/lang) for the full set of locales currently supported.

You can help improve ZAP translations via [https://crowdin.com/project/owasp-zap](https://crowdin.com/project/owasp-zap). 
