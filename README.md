## 2- Vault KV Secret Engine Versions Difference


### **Version 1**

```yaml
#Enable kv in default path
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets enable kv
Success! Enabled the kv secrets engine at: kv/

#Enable kv in SRE_TEAM/
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets enable --path=SRE_TEAM kv
Success! Enabled the kv secrets engine at: SRE_TEAM/

#Enable kv in SRE_TEAM-v1
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets enable -version=1 --path=SRE_TEAM-v1 kv
Success! Enabled the kv secrets engine at: SRE_TEAM-v1/
```

![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/eaeb5e6b-b9d6-429d-b907-14d9ffaae54d)


### **Version 2**

```yaml
#Enable kv in DB_Team/ using version 2
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets enable -version=2 --path=DB_Team kv
Success! Enabled the kv secrets engine at: DB_Team/

#Enable kv in SRE_TEAM-v2/ using version 2
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets enable -version=2 --path=SRE_TEAM-v2 kv
Success! Enabled the kv secrets engine at: SRE_TEAM-v2/

myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault secrets list
Path            Type         Accessor              Description
----            ----         --------              -----------
DB_Team/        kv           kv_779fc707           n/a
SRE_TEAM-v1/    kv           kv_46705640           n/a
SRE_TEAM-v2/    kv           kv_c0855368           n/a
SRE_TEAM/       kv           kv_2aa2c1c6           n/a
cubbyhole/      cubbyhole    cubbyhole_63a670bf    per-token private secret storage
identity/       identity     identity_bb56535d     identity store
kv/             kv           kv_9f4992a5           n/a
secret/         kv           kv_966419c8           key/value secret storage
sys/            system       system_59797446       system endpoints used for control, policy and debugging
```

![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/6b459af8-59e4-485b-9156-82c1377badd0)



### Difference between Version1 & Version 2

[Static secrets: Key/value secrets engine | Vault | HashiCorp Developer](https://developer.hashicorp.com/vault/tutorials/secrets-management/static-secrets)

<aside>
💡 The KV secrets engine v1 does not provide a way to version or roll back secrets. This made it difficult to recover from unintentional data loss or overwrite when more than one user is writing at the same path.

</aside>

![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/fb539ae1-1856-42df-b9ae-a725b8105a5d)


<aside>
💡 The KV secrets engine v2 can retain a configurable number of secret versions. This enables older versions' data to be retrievable in case of unwanted deletion or updates of the data. In addition, its *Check-and-Set* operations can be used to protect the data from being overwritten unintentionally.

</aside>

![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/815d3d05-9ea5-4549-ab65-fd79d641cf17)


![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/dd94a8e8-c9f0-4ef4-b340-ba90db459cef)


![image](https://github.com/myathway-lab/2-Vault-KV-Versions-Diff/assets/157335804/a26b46d7-d7ec-436b-84e4-6e5f1325fbd7)


```yaml
myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault kv get SRE_TEAM-v1/user1
====== Data ======
Key         Value
---         -----
location    US
name        myamya

myathway@DESKTOP-QCOTPM5:/mnt/c/WINDOWS/system32$ vault kv get SRE_TEAM-v2/userinfo
====== Secret Path ======
SRE_TEAM-v2/data/userinfodisk
   25  cd
======= Metadata =======
Key                Value
---                -----s
created_time       2024-02-27T15:08:25.370061Z
custom_metadata    <nil>
deletion_time      n/a
destroyed          false
version            51
   34  mkdir .ssh
==== Data ====/
Key     Valueygen
---     -----
name    myamyagen
```

**API endpoint that the CLI command invokes are slightly different beteween KV v1 and KV v2.**

**The following table lists the `vault kv` subcommands and their respecting API endpoint. This assumes that the KV secrets engine is enabled at `secret/`.**

| Command | KV v1 endpoint | KV v2 endpoint |
| --- | --- | --- |
| vault kv get | secret/<key_path> | secret/data/<key_path> |
| vault kv put | secret/<key_path> | secret/data/<key_path> |
| vault kv list | secret/<key_path> | secret/metadata/<key_path> |
| vault kv delete | secret/<key_path> | secret/data/<key_path> |

**In addition, KV v2 has additional subcommands that are not available for KV v1.**

| Command | KV v2 endpoint |
| --- | --- |
| vault kv patch | secret/data/<key_path> |
| vault kv rollback | secret/data/<key_path> |
| vault kv undelete | secret/undelete/<key_path> |
| vault kv destroy | secret/destroy/<key_path> |
| vault kv metadata | secret/metadata/<key_path> |

###
