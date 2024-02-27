[![Build and Test](https://github.com/milliewalky/setup-rclone/actions/workflows/sample.yml/badge.svg)](https://github.com/milliewalky/setup-rclone/actions/workflows/sample.yml)

# Setup rclone

This action downloads, unpacks, and configures rclone for use in GitHub Actions workflows. rclone is a command-line program to manage files on cloud storage. It can synchronize files between your file system and cloud storage services. rclone is unpacked under the temporary directory of a runner.

Utilises REST API.

# Usage

<!-- start usage -->
```yaml
- uses: milliewalky/setup-rclone@v1
  with:
    # rclone configuration file encoded in base64. if provided, the action will decode this and create an `rclone.conf` file for use with rclone commands.
    # default: ""
    conf: ""

    # GitHub PAT fed so that you are not rate-limited.
    # default: {{ github.token }}
    token: ""

    # Rclone release tag from their GitHub Releases page.
    # default: latest
    tag: ""
```
<!-- end usage -->

## Encoding `rclone.conf` in base64

You can encode `rclone.conf` file w/ certutil:

```
certutil -encode rclone.conf rclone.conf.base64
```

If everything was run correctly, you should have output very similar to the
following:

```
-----BEGIN CERTIFICATE-----
SGVyZSBpcyB0aGUgbGFzdCBvZiB0aGUgUmVkLCBhbmQgdGhlIGJlZ2lubmluZyB0
byBwdXQgYXdheSB0aGUgZGVhZC4gVGhlIEVsaXhpciBWaXRhZS4NCg0KWy4uLl0N
Cg0KVGhlIGJpcmQgb2YgSGVybWVzIGlzIG15IG5hbWUgZWF0aW5nIG15IHdpbmdz
IHRvIG1ha2UgbWUgdGFtZS4NCg==
-----END CERTIFICATE-----
```

Remove newline characters and the very first, and the last line:

```
SGVyZSBpcyB0aGUgbGFzdCBvZiB0aGUgUmVkLCBhbmQgdGhlIGJlZ2lubmluZyB0byBwdXQgYXdheSB0aGUgZGVhZC4gVGhlIEVsaXhpciBWaXRhZS4NCg0KWy4uLl0NCg0KVGhlIGJpcmQgb2YgSGVybWVzIGlzIG15IG5hbWUgZWF0aW5nIG15IHdpbmdzIHRvIG1ha2UgbWUgdGFtZS4NCg==
```

Finally, copy the string and then—on GitHub—go to `Settings` → `Secrets and variables` → `Actions and create`, click `New repository secret` button, paste the string and name it whatever you like e.g. `RCLONE_CONF_BASE64`. Then, when setting you the workflow:

```yaml
- uses: milliewalky/setup-rclone@v1
  with:
    conf: ${{ secrets.RCLONE_CONF_BASE64 }}
```

NOTE: The rclone.conf file will be created in the same directory where rclone was installed. If you intend to use this configuration file, you must pass it as an argument when running any rclone command that interacts with your chosen remote, e.g.:

```
rclone copy {src} {remote}:{dst} --config=path/to/rclone.conf
```

Since this action append rclone to a temporary PATH file, you might want to query for rclone location so that you know the location of `rclone.conf` file as well e.g. with a sample Python code:

```python
import shutil

print(f"{shutil.which("rclone")}")
```

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
