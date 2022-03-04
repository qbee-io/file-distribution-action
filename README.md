# file-distribution-action
Send a file distribution configuration to the qbee.io platform within GitHub runners.

# Usage
To use this action make sure to use our [authentication action](https://github.com/qbee-io/authenticate-action) before the file upload such that the authorization token can be passed.

A sample GitHub action file in your repository would look like this

```yaml
name: qbee.io file distribution
on:
 push:
    branches: [ main ]

jobs:
 build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: qbee.io authentication
      uses: qbee-io/authenticate-action@v1
      id: qbee-auth
      with:
          login: ${{ secrets.USERNAME_KEY }}
          password: ${{ secrets.PASSWORD_KEY }}

    - name: qbee.io file distribution
      uses: qbee-io/file-distribution-action@v1
      with:
          token: ${{ steps.qbee-auth.outputs.token }}
          config_file: 'qbee/file_distribution.json'
          mode: 'if_not_present'
          device_or_group_id: 'root'
          commit_message: 'file-distribution-action test'
```
Where the GitHub repository contains a folder `qbee` with a config file for the file distribution `file_distribution.json` that looks like this (for example):
```json
{
  "enabled": true,
  "files": [
    {
      "templates": [
        {
          "source": "/file_to_upload.txt",
          "destination": "/home/user/destination_file.txt",
          "is_template": false
        },
        {
          "source": "/file_to_upload2.txt",
          "destination": "/home/user/destination_file2.txt",
          "is_template": false
        }
      ],
      "command": "ls -als /home/user/"
    },
    {
      "templates": [
        {
          "source": "/script_to_run.py",
          "destination": "/home/user/destination_script.py",
          "is_template": false
        }
      ],
      "command": "python3 /home/user/destination_script.py &"
    }
  ],
  "version": "v1"
}
```

The usage of [qbee-io/authenticate-action](https://github.com/qbee-io/authenticate-action) is explained on its own documentation page.

To upload the files used within the file distribution configuration use [qbee-io/file-upload-action](https://github.com/qbee-io/file-upload-action)

# Input variables

* `token`: authentication token obtained from the previous step
* `config_file`: the `file distribution` configuration file as `json` to be applied to the specified devices
* `mode`: how to perform the file distribution configuration
    * `add`: append configuration to existing file distribution
    * `replace`: replace existing file distribution (override)
    * `if_not_present`: only upload the configuration is no file distribution is present for the select devices
* `device_or_group_id`: id of the group or device for which the configuration should be applied; default `root` (all devices)
* `commit_message`: message for the commit visible in the qbee.io audit; default: 'file distribution update via GitHub'

