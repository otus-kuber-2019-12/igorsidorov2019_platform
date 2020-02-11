# igorsidorov2019_platform
igorsidorov2019 Platform repository

## kubernetes-intro

* create the Dockerfile and exec local tests;
* build and push docker image to the Docker Hub;
* create Kubernetes manifest for my web app;
* pull HipsterShop repo and create docker image from current Dockerfile (frontend);
* push new image to Docker Hub;
* create Kubernetes manifest for frontend app and exec it;
* fix error of frontend app (add envs).

## kubernetes-controllers

* study info about replicaset and create some replicasets;
* study info about deployments and create some deployments;
* study info about deamonsets and create some deamonsets.

## kubernetes-security

* study info about accounting, roles, roles binding.

## kubernetes-networks

* learn info about network infrastructure

## kubernetes-volume

* learn info about volumes

## kubernetes-template

* learn info about templating

## kubernetes-operator

[13:39:06] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           2s         19m
restore-mysql-instance-job   0/1           47m        47m

[13:38:35] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data-1' );" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.

[13:38:56] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data-2 |
|  2 | some data-1 |
+----+-------------+

## kubernetes-monitoring

learn info about monitoring at kuber

## kubernetes-vault

helm status vault
```
NAME: vault
LAST DEPLOYED: Sun Feb  9 13:14:47 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://www.vaultproject.io/docs/


Your release is named vault. To learn more about the release, try:

  $ helm status vault
  $ helm get vault
```

kubectl exec -it vault-0 -- vault operator init --key-shares=1 --key-threshold=1
```
Unseal Key 1: i9F8ldEd2dT+xWS/hUjoF/zKH7/6CjQoWQ3eE6VCzE4=

Initial Root Token: s.Src0vFjdqOzR9brj7kOYwuc2

Vault initialized with 1 key shares and a key threshold of 1. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 1 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 1 key to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```

k exec -it vault-0 -- vault operator unseal "i9F8ldEd2dT+xWS/hUjoF/zKH7/6CjQoWQ3eE6VCzE4="
```
Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           1
Threshold              1
Version                1.3.1
Cluster Name           vault-cluster-3a482183
Cluster ID             82ccfbc1-33ad-f94c-b682-d9d9c93d2a11
HA Enabled             true
HA Cluster             n/a
HA Mode                standby
Active Node Address    <none>
```

k exec -it vault-0 --  vault login
```
Token (will be hidden): 
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.Src0vFjdqOzR9brj7kOYwuc2
token_accessor       gcE0P1EFLk381eJfTdv2XqmU
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```

k exec -it vault-0 --  vault auth list
```
Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_cdbb2a5a    token based credentials
```

kubectl exec -it vault-0 -- vault kv get otus/otus-rw/config
```
====== Data ======
Key         Value
---         -----
username    otuspassword=asajkjkahs
```

k exec -it vault-0 --  vault auth list
```
Path           Type          Accessor                    Description
----           ----          --------                    -----------
kubernetes/    kubernetes    auth_kubernetes_30e6c0d0    n/a
token/         token         auth_token_cdbb2a5a         token based credentials
```

kubectl exec -it vault-0 -- vault write pki_int/issue/example-dot-ru common_name="test.example.ru" ttl="24h"
```
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUeQNqBmcvR3YoMoWuggFiOB0EbUQwDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMDAyMDkyMDI5NDdaFw0yNTAy
MDcyMDMwMTdaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOvEmJVh4tcZ
FcQrVL/fpSCICZMH85CdMYHuxDuX6sjW+Tr7mmreji0Vbr8e/+8BOv4YZ0tC93T3
n71VwWzjyoHrzy6mOtJfwd3Kzm+exjqwiCCVW+W83DbZdhcV7fLY1W8akMru1RcP
8VT1iLJKOiV1UbUkz7uxZqfzC8p4AztJhF5iEdSCCy1J7G0MshQy6vkONh2Gxoii
s1sb7WUiyUwXAzZmmCa7Xs1VbbkLFHDt54DEVwbCZKQIzfHf4M5pvhJt+V2a2W5d
oEQv7aVJcw627saFL/vHtFdNqoRdzrxOFZyRe/N4oVlat+Ech1fLWp5NByj11jGN
z6LwPMc7VWsCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUt9VZNw7HHZClTIaKII3TjfRFHdowHwYDVR0jBBgwFoAU
wmuwfyiRd6vzy0yQv6iUaLaXEi8wNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
puIKa/u7t7yEeddrlXdsFOXUP77n5e3zgFZ/CxO4p9cGKrW2t7OuyIpN8HP11EmI
PN0CvqYOeasdV++yEX6Zde05bv1K4swXibvFa/Nfq8xHpXRoDAChbORyvafOIRxI
cKDLt4S+rjej4vK1G2ru0VjhJFZZbdYsGNN4BIGDnIjlAc3Lgl/HRl6dk27FOROR
kzri0QvCq/6aYgfOmlLH45wqY+vGXtu74ckq2MMjgz020bXgL5tljFFhBPBBJoHJ
7sBnWxju3DJD91I6m+jacRJ7G84HomNgkz0hE0VCaHpo6XyVX19dZbrvPIT2DuLd
WLbxv9i6OxhuVwhSqlP0SA==
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDYzCCAkugAwIBAgIUaTY78cXA0IIRZtEECaMQ0Zzb8GowDQYJKoZIhvcNAQEL
BQAwLDEqMCgGA1UEAxMhZXhhbXBsZS5ydSBJbnRlcm1lZGlhdGUgQXV0aG9yaXR5
MB4XDTIwMDIwOTIwNDE1N1oXDTIwMDIxMDIwNDIyNlowGjEYMBYGA1UEAxMPdGVz
dC5leGFtcGxlLnJ1MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAsImK
irqGmCf0Ci6TVEkhJn6GzcPXKszC7tl9BNwpJr6qQhp3gkHwF6g+mVZvOse/tLRZ
NqRNQ/Ygg6Sx7jWXl+F+GFOSGwIRhfEkmzO/FDY2Bm1wWXOwJfuBJolSsxvz1Ldg
rm70Q6V2XlwWlX4gRcevEYTrFV23uo/iDmbtBR/8TL4XkeLGYyF4hB5r6ALewnyv
Hpu1zqntJBssGRnr0s1AJfPSeSAp3PPnjD3emR1HnfLPkkvdkx5gaRRaRHSpw7nT
lLeMNm69OHIhvGV5vGqmrObunjX2Jpm1uYyJkKTva2rnPUEOGeZL5DnAFPrzVQUK
C25j6HFVSyZJmOR1XwIDAQABo4GOMIGLMA4GA1UdDwEB/wQEAwIDqDAdBgNVHSUE
FjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwHQYDVR0OBBYEFKZ6DJ0ncMBO04AFBqqL
QxNWAtO8MB8GA1UdIwQYMBaAFLfVWTcOxx2QpUyGiiCN0430RR3aMBoGA1UdEQQT
MBGCD3Rlc3QuZXhhbXBsZS5ydTANBgkqhkiG9w0BAQsFAAOCAQEAtYDFoPrAh1+B
cCZzQsWy+eYEIaV3hBst/PpeKU5dBIbwIZE5tF8G97iU1lz1hNbkhY81wRbecyxd
Qwi1PH4l62pSb9HJnaCrxcbzeQmytnJ7JflIRs8e37aSVnTJ5+60YZc5XwrfuRfR
tM33/vyCjBCuAVJe/pzD4PHh1Ks6DHmbfz/5sEO7XWspm5zq1WBhPTOyb0JXaUgN
HWFCDGLJtVEqUCHoRqsSMvQtXj4Xq2bNqDh2ksb5N4LeqP75w4qg62URZajCjc7G
pp/xp2IoapMbQ0NM2ZZwsWh0J6c2U6H3u9gBCkJrs6oZaRj8yFIBASjo9o9FnxQ3
9FqzHTRv5g==
-----END CERTIFICATE-----
expiration          1581367346
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUeQNqBmcvR3YoMoWuggFiOB0EbUQwDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMDAyMDkyMDI5NDdaFw0yNTAy
MDcyMDMwMTdaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOvEmJVh4tcZ
FcQrVL/fpSCICZMH85CdMYHuxDuX6sjW+Tr7mmreji0Vbr8e/+8BOv4YZ0tC93T3
n71VwWzjyoHrzy6mOtJfwd3Kzm+exjqwiCCVW+W83DbZdhcV7fLY1W8akMru1RcP
8VT1iLJKOiV1UbUkz7uxZqfzC8p4AztJhF5iEdSCCy1J7G0MshQy6vkONh2Gxoii
s1sb7WUiyUwXAzZmmCa7Xs1VbbkLFHDt54DEVwbCZKQIzfHf4M5pvhJt+V2a2W5d
oEQv7aVJcw627saFL/vHtFdNqoRdzrxOFZyRe/N4oVlat+Ech1fLWp5NByj11jGN
z6LwPMc7VWsCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUt9VZNw7HHZClTIaKII3TjfRFHdowHwYDVR0jBBgwFoAU
wmuwfyiRd6vzy0yQv6iUaLaXEi8wNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
puIKa/u7t7yEeddrlXdsFOXUP77n5e3zgFZ/CxO4p9cGKrW2t7OuyIpN8HP11EmI
PN0CvqYOeasdV++yEX6Zde05bv1K4swXibvFa/Nfq8xHpXRoDAChbORyvafOIRxI
cKDLt4S+rjej4vK1G2ru0VjhJFZZbdYsGNN4BIGDnIjlAc3Lgl/HRl6dk27FOROR
kzri0QvCq/6aYgfOmlLH45wqY+vGXtu74ckq2MMjgz020bXgL5tljFFhBPBBJoHJ
7sBnWxju3DJD91I6m+jacRJ7G84HomNgkz0hE0VCaHpo6XyVX19dZbrvPIT2DuLd
WLbxv9i6OxhuVwhSqlP0SA==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAsImKirqGmCf0Ci6TVEkhJn6GzcPXKszC7tl9BNwpJr6qQhp3
gkHwF6g+mVZvOse/tLRZNqRNQ/Ygg6Sx7jWXl+F+GFOSGwIRhfEkmzO/FDY2Bm1w
WXOwJfuBJolSsxvz1Ldgrm70Q6V2XlwWlX4gRcevEYTrFV23uo/iDmbtBR/8TL4X
keLGYyF4hB5r6ALewnyvHpu1zqntJBssGRnr0s1AJfPSeSAp3PPnjD3emR1HnfLP
kkvdkx5gaRRaRHSpw7nTlLeMNm69OHIhvGV5vGqmrObunjX2Jpm1uYyJkKTva2rn
PUEOGeZL5DnAFPrzVQUKC25j6HFVSyZJmOR1XwIDAQABAoIBADSFlyxS9jxKlrZ4
yOhooW48corBW0CmjtBU8HcDsVKPo0Prb+bXC0FektE3//uy9fwjM++nzj0L+vxv
gWhnlWRgeA1wv6U9Zk09QzDRomW3AzfCU4ypeJy0SOZYMLyM8mw06pS6+f0uOxsz
tpxfW+am1BUYQICo00K0EANM5/N/5CDu5BA/xG7KFtauP3enTr0zGFSNOB6y8qBW
fcrAYIG5ds6YqyFSttbWTcnB46SPuPEjdGfZHMJMu8SLn9Wc3ZbUsajoTdmlm4hW
hj9bJDtrXOtZoOsImuA1mGdqIWeWFMrnjCMM5u9OuTiu/3yKpBzXJDVNNNLXmoqm
qVHGP0ECgYEA2M9Jw5BSAOJ9EZTapH0wr3Q92koJZn2ZX3yqaeBKzcU6c+bBY8aH
ppFIezX5rlgy6i9E9syUFwxyBqrcUPm2x0Y6LUOuS/DcrzR48grPwu/qxcjg5Dbq
jCgML0s66MWRaNFkdYJCiEGDOW+ndACELgt0IakgPybcIsFiEDP2008CgYEA0HKs
vvHJODiiBHPwECFJDfnnQ3A3E67tS9T1heNwQMQnQoRe0Fr4nnrKKhWe5TL68ORk
uJ001vIbHE5jrIdP8sZdKj9dPnXk9yu6mmmJsVsXfn0N8OxogqQVg1GwfdHiVmc8
UHPKyudpZMJZGkXAKACMnR6d8X9uNfLQOZk9+PECgYEArIGaTnlZVgzfuIp4wSI/
B4t032fDPQI4c4psyVtGCZ2hGbEENNA1BKpaQna62CajNEQyGjDCr+geHgH61I8s
CDhvd65/UzstTFZy2RsTHibo5UAk+FBdpPEEaOjx0V3Jid35kan4KBQARkX5tcnn
Yf+JAnNgDf9sblbyILRH8u8CgYAmc1DMNBuTBFdWjPBWeV1Zd6SiOvvd5KGfIFxd
4zNcrxIy4en/cxhzW2EZXD2gN8Q0VV0C9PS/RY+crBUUyS0FMnQTC/cuQ18F/QoB
27/reEsgKP8+Vs18c7oILDRrMSEYIRjuGj3pKcC2NmdrQjyM5HULso8d8gypZO3m
Ag99cQKBgFQgLnSoye9WYvPqecozj9r87COBQNZZixiXsBDLDWPvzUe3cNlY93Nk
bmGMCmTx5bW74Y8FcEbD2XnVLmsdbGbGyrY6squb98mggHeH9bbabzgduV0r8xNJ
Xr7zwUPI7zNN5HcmG/YqxdYeRLl9eMp1mloc1bYRv1FLTyzCrBFN
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       69:36:3b:f1:c5:c0:d0:82:11:66:d1:04:09:a3:10:d1:9c:db:f0:6a
```

kubectl exec -it vault-0 -- vault write pki_int/revoke serial_number="69:36:3b:f1:c5:c0:d0:82:11:66:d1:04:09:a3:10:d1:9c:db:f0:6a"
```
Key                        Value
---                        -----
revocation_time            1581281098
revocation_time_rfc3339    2020-02-09T20:44:58.608426364Z
```


