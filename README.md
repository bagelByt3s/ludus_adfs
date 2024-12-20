# ADFS Collection for Ansible and [Ludus](https://ludus.cloud)

This collection includes Ansible roles to install ADFS. For a good example of the collection's usage, see the `ADFS-Range.yml`.

Roles included in this collection:
  - `bagelByt3s.ludus_adfs.import_root_cert`
  - `bagelByt3s.ludus_adfs.install_adfs`
  - `bagelByt3s.ludus_adfs.entra_prep`

Associated Blogpost: https://medium.com/specter-ops-posts/adfs-entra-lab-with-ludus-9bffbc51673f

## Installation in [Ludus](ludus.cloud)

Install via Ansible Galaxy:
ludus ansible collection add bagelbyt3s.ludus_adfs

Install via git:

```
git clone https://bagelByt3s/ludus_adfs /opt/ludus_adfs
ludus ansible collection add /opt/ludus_adfs
```

### Role Requirements

None

## Usage
Example Ludus configuration

```yaml
ludus:
  - vm_name: "DC01-WinServer2022"
    hostname: "DC01"
    template: win2022-server-x64-template
    vlan: 10
    ip_last_octet: 10
    ram_gb: 8
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: ludus.nuketown
      role: primary-dc
    roles:
      - bagelbyt3s.ludus_adfs.install_adcs

  - vm_name: "ADFS-WinServer2022"
    hostname: "ADFS"
    template: win2022-server-x64-template
    vlan: 10
    ip_last_octet: 11
    ram_gb: 8
    ram_min_gb: 2
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: ludus.nuketown
      role: member
    roles:
      - bagelbyt3s.ludus_adfs.import_root_cert
      - bagelbyt3s.ludus_adfs.install_adfs
    role_vars:
      adfs_service_account: 'adfs_svc'
      adfs_service_account_password: 'password'
      adfs_service_display_name: 'ADFS Service'
      adfs_hostname: 'adfs.ludus.nuketown'
      adfs_FQDN_CA: 'DC01.ludus.nuketown\ludus-CA'
      adfs_CA: "ludus-CA"
      adfs_certificate_subject: "CN=adfs.ludus.nuketown, O=Ludus, C=US"

  - vm_name: "EntraConnect-WinServer2022"
    hostname: "EntraConnect"
    template: win2022-server-x64-template
    vlan: 10
    ip_last_octet: 12
    ram_gb: 8
    ram_min_gb: 2
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: ludus.nuketown
      role: member
    roles:
      - bagelbyt3s.ludus_adfs.import_root_cert
      - bagelbyt3s.ludus_adfs.entra_prep
    role_vars:
      adfs_CA: "ludus-CA" 
      ludus_entra_join_svc_account: "entra_svc"
      ludus_entra_join_svc_account_password: "password"
      ludus_entra_join_alt_upn: "" #Example: domain.onmicrosoft.com or leave blank to skip

  - vm_name: "Workstation-Win11"
    hostname: "Workstation"
    template: win11-22h2-x64-enterprise-template
    vlan: 10
    ip_last_octet: 13
    ram_gb: 4
    ram_min_gb: 2
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: ludus.nuketown
      role: member
    roles:
      - bagelbyt3s.ludus_adfs.import_root_cert
    role_vars:
      adfs_CA: "ludus-CA"

```

Then set the config and deploy it

```
ludus range config set -f ADFS-Range.yml
ludus range deploy
```

## License

GPLv3

## Author

This collection was created by [Beyviel David](https://www.linkedin.com/in/beyviel-david-793585164) and [Erik Hunstad](https://github.com/kernel-sanders), for [Ludus](https://ludus.cloud).


