# csp-client-installer GitHub Action

This action enables you to download and install the latest Venafi CodeSign Protect client (available for your version of Trust Protection Platform) in preparation for performing code signing operations with the Venafi CodeSign Protect platform.

## Usage

This action currently supports GitHub-provided Linux, macOS and Windows runners (self-hosted runners may not work)

Add the following entry to your Github workflow YAML file:

```yaml
uses: venafi/csp-client-installer@main
with:
  tpp-url: 'https://tpp.example.com'
```

Example of a simple workflow:

```yaml
jobs:
  test_vsign_action:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write
      id-token: write # needed for signing the images with GitHub OIDC Token **not production ready**

    name: Install Venafi CSP client
    steps:
      - uses: actions/checkout@master
        with:
          fetch-depth: 1

      - name: Install Venafi CSP client
        uses: venafi/csp-client-installer@main

      - name: Install Java
      - uses: actions/setup-java@v3
        with:
            java-version: '11'
      - name: Download resources
        run: |
          wget https://my.repo/test.apk -O ~/test.apk
          wget https://my.repo/venafipkcs11.txt -O ~/venafipkcs11.txt

      - name: Sign JAR file
        run: |
          jarsigner -verbose -keystore NONE -storetype PKCS11 -storepass none -providerclass sun.security.pkcs11.SunPKCS11 -providerArg ~/venafipkcs11.txt -certs -tsa http://timestamp.digicert.com ~/test.apk my-signing-cert'
```

### Optional Inputs
The following optional inputs:

| Input | Description |
| --- | --- |
| `use-sudo` | set to `true` if `install-dir` location requires sudo privs. Defaults to false. |