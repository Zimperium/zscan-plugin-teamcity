# zscan-plugin-teamcity

## TeamCity Job script for uploads to zScan

This script can be used to upload mobile applications to Zimperium (zScan) to be scanned for vulnerabilities. Using this script simplifies integrating mobile application security testing into CI/CD process and enables detection and remediation of vulnerabilities earlier in the application SDLC.

For more information on zScan, please see [Continuous Mobile Application Security Scanning](https://www.zimperium.com/zscan/).

## Prerequisites

1. Zimperium [MAPS](https://www.zimperium.com/mobile-app-protection/) license that includes zScan functionality.
2. A valid application binary (.ipa, .apk, etc.), either built by the current pipeline or otherwise accessible by the script.
3. API credentials with permissions to upload binaries. In your console, head over to the Authorizations tab in the Account Management section and generate a new API key. At a minimum, the following permissions are required:

- Common Section: Teams - Manage
- zScan Section: zScan Apps - Manage, zScan Assessments - View, zScan Builds - Upload

## Parameters

We recommend using TeamCity [Parameters](https://www.jetbrains.com/help/teamcity/levels-and-priority-of-build-parameters.html#Name+Restrictions) to provide parameters to the script. Select "Environment Variable" for the Kind, and select "Text", "Password", etc. for the Value Type, as needed.

### Mandatory

These parameters are mandatory, _unless_ a default value is available as described below.

- **ZSCAN_SERVER_URL**: console base URL, e.g., `https://ziap.zimperium.com/`.
- **ZSCAN_CLIENT_ID** and **ZSCAN_CLIENT_SECRET**: API credentials that can be obtained from the console (see the [Prerequisites](#prerequisites) section above).
- **ZSCAN_INPUT_FILE**: the path to the binary relative to the current workspace.
- **ZSCAN_TEAM_NAME**: name of the team to which this application belongs.  This is required only if submitting the application for the first time; values are ignored if the application already exists in the console and assigned to a team.  If not supplied, the application will be assigned to the 'Default' team
- **ZSCAN_REPORT_FORMAT**: the format of the scan report, either 'json' or 'sarif' (default).  If you plan on importing zScan results into GitLab Ultimate edition, please use sarif format.  For more information on the SARIF format, please see [OASIS Open](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html).

### Optional

These parameters are optional, but may be used to supply additional information about the build and/or control the plugin's output.

- **ZSCAN_REPORT_LOCATION**: destination folder for the vulnerability report. If not provided, the report is stored in the current workspace. Report location and name are important for [Build Artifact](https://www.jetbrains.com/help/teamcity/build-artifact.html) collection.
- **ZSCAN_REPORT_FILE_NAME**: filename of the report. If not provided, the filename will be patterned as follows: zscan-results-AssessmentID-report_format.json, e.g., _zscan-results-123456789-sarif.json_.
- **ZSCAN_WAIT_FOR_REPORT**: if set to "true" (default), the script will wait for the assessment report to be complete. Otherwise, the script will exit after uploading the binary to zScan.  The assessment report can be obtained through the console. Report filename and location parameters are ignored. No artifact will be produced.
- **ZSCAN_POLLING_INTERVAL**: wait time for polling the server in seconds. 30 seconds is the default.
- **ZSCAN_BRANCH**: source code branch that the build is based on. Can be set to one of TeamCity [Predefined Variables](https://www.jetbrains.com/help/teamcity/predefined-build-parameters.html), e.g., _teamcity.build.branch_.
- **ZSCAN_BUILD_NUMBER**: application build number. Can be set to one of TeamCity [Predefined Variables](https://www.jetbrains.com/help/teamcity/predefined-build-parameters.html), e.g., _system.build.number_.
- **ZSCAN_ENVIRONMENT**: target environment, e.g., uat, dev, prod.

## Usage

Please refer to [TeamCity Documentation](https://www.jetbrains.com/help/teamcity/command-line.html) for instructions on using command-line scripts as build steps.  In your Build Configuration, go to the Build Steps section and add a new Build Step.  Select "Command Line" as the runner. Make sure the step is set to execute _after_ the step that builds your application. Paste the following lines into the "Custom Script" section and modify as needed:

```bash
 wget -O zScan.sh https://raw.githubusercontent.com/Zimperium/zscan-plugin-teamcity/refs/heads/master/zScan_v1.sh
 chmod +x zScan.sh
 ./zScan.sh
```

As mentioned above, if the script is configured not to wait for an assessment report, no artifacts will be produced. Otherwise, you can add zScan report to the list of artifacts produced by the build.  Go to the General Settings section, and another line to the Artifact Paths section.  The exact configuration depends on the value(s) specified for the ZSCAN_REPORT_LOCATION and ZSCAN_REPORT_FILE_NAME parameters.

## License

This script is licensed under the MIT License. By using this plugin, you agree to the following terms:

```text
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Enhancements

Submitting improvements to the plugin is welcomed and all pull requests will be approved by Zimperium after review.
