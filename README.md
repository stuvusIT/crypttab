# Role Name

This roles writes `/etc/crypttab`, saves the respective LUKS headers and rebuilds the initrd after changes.


## Requirements

This role has been tested on Debian 10, but should work on any distribution that uses `/etc/crypttab` and `systemd` (the daemon will be reexeced after changes).

## Role Variables


| Name                         | Required/Default           | Description                                                                                   |
|------------------------------|:--------------------------:|-----------------------------------------------------------------------------------------------|
| `crypttab_devices`           | `{}`                       | Dictionary to specify crypto devices to unlock, see below.                                    |
| `crypttab_default_password`  | `none`                     | Value to write into the `password` column if none is specified for a crypto device.           |
| `crypttab_default_options`   | `['luks']`                 | Default list of options to specify in the last column                                         |
| `crypttab_initrd_command`    | `update-initramfs -uk all` | Command that is executed after crypttab changes to rebuild the initrd                         |
| `crypttab_backup_headers`    | `True`                     | Whether to backup the LUKS headers to the directory specified in `crypttab_header_backup_dir` |
| `crypttab_header_backup_dir` | `/boot/luks-headers`       | Directory to backup LUKS headers to if `crypttab_backup_headers` is set                       |

### Crypttab devices

Each entry in the `crypttab_devices` dict configures one crypto device.
The key specifies the crypto name, the value is another dict with the following content:

| Name       | Required/Default                  | Description                                                                   |
|------------|:---------------------------------:|-------------------------------------------------------------------------------|
| `device`   | :heavy_check_mark:                | Path to the device to unlock. You may also specify something like `UUID=xyz`. |
| `password` | `{{ crypttab_default_password }}` | Value to write into the `password` column                                     |
| `options`  | `{{ crypttab_default_options }}`  | List of options to specify in the last column                                 |

## Example

```yml
crypttab_initrd_command: dracut --force
crypttab_devices:
  root_ssd:
    device: /dev/sda2
    options:
      - luks
      - discard
crypttab_header_backup_dir: /mnt/backup/luks-headers
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

- [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
