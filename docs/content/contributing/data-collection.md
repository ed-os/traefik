# Data Collection

Understanding How Traefik is Being Used
{: .subtitle }

## Configuration Example

Understanding how you use Traefik is very important to us: it helps us improve the solution in many different ways.  
For this very reason, the sendAnonymousUsage option is mandatory: we want you to take time to consider whether or not you wish to share anonymous data with us so we can benefit from your experience and use cases.

!!! warning
    During the alpha stage only, leaving this option unset will not prevent Traefik from running but will generate an error log indicating that it enables data collection by default.

??? example "Enabling Data Collection with TOML"

    ```toml
    [Global]
        # Send anonymous usage data
        sendAnonymousUsage = true
    ```

??? example "Enabling Data Collection with the CLI"

    ```bash
    ./traefik --sendAnonymousUsage=true
    ```
    
## Collected Data

This feature comes from the public proposal [here](https://github.com/containous/traefik/issues/2369).

In order to help us learn more about how Traefik is being used and improve it, we collect anonymous usage statistics from running instances.
Those data help us prioritize our developments and focus on what's important for our users (for example, which provider is popular, and which is not).

### What's collected / when ?

Once a day (the first call begins 10 minutes after the start of Traefik), we collect:

- the Traefik version number
- a hash of the configuration
- an **anonymized version** of the static configuration (token, user name, password, URL, IP, domain, email, etc, are removed).

!!! note
    We do not collect the dynamic configuration information (routers & services).
    We do not collect these data to run advertising programs.
    We do not sell these data to third-parties.

### Example of Collected Data

??? example "Original configuration"

    ```toml
    [entrypoints]
        [entrypoints.web]
           address = ":80"
    
    [api]
    
    [Docker]
      endpoint = "tcp://10.10.10.10:2375"
      domain = "foo.bir"
      exposedByDefault = true
      swarmMode = true
    
      [Docker.TLS]
        ca = "dockerCA"
        cert = "dockerCert"
        key = "dockerKey"
        insecureSkipVerify = true
    
    [ECS]
      domain = "foo.bar"
      exposedByDefault = true
      clusters = ["foo-bar"]
      region = "us-west-2"
      accessKeyID = "AccessKeyID"
      secretAccessKey = "SecretAccessKey"
    ```

??? example "Resulting Obfuscated Configuration"

    ```toml
    [entrypoints]
        [entrypoints.web]
           address = ":80"
    
    [api]
    
    [Docker]
      endpoint = "xxxx"
      domain = "xxxx"
      exposedByDefault = true
      swarmMode = true
    
      [Docker.TLS]
        ca = "xxxx"
        cert = "xxxx"
        key = "xxxx"
        insecureSkipVerify = false
    
    [ECS]
      domain = "xxxx"
      exposedByDefault = true
      clusters = []
      region = "us-west-2"
      accessKeyID = "xxxx"
      secretAccessKey = "xxxx"
    ```

## The Code for Data Collection

If you want to dig into more details, here is the source code of the collecting system: [collector.go](https://github.com/containous/traefik/blob/master/pkg/collector/collector.go)

By default we anonymize all configuration fields, except fields tagged with `export=true`.
