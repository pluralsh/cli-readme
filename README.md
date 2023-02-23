# Welcome to Your Plural Repo

This is the repo that will house all your plural installations for this cluster.  We'll give you a quick walkthrough how everything works here.  In general, there are two main steps w/ plural:

```
plural build -- generates infrastructure as code for your installations
plural deploy -- deploys your IAC resources in dependency-order
```


If you want to install a new app, you can run:

```
plural bundle install APP BUNDLE
```

and you can discover bundles for an app with `plural bundle list APP`. To traverse our marketplace, run `plural repos list` or go to https://app.plural.sh

If you want an at-a-glance view of the health of any application, you can run:

```
plural watch APP
```

but the best way to manage and monitor your applications is with the plural console, which you can install with `plural bundle install console console-{aws,gcp,azure,etc}`.  We strongly recommend doing this.

## Workspace Structure

We generate infrastructure as code resources in a consistent format:

```
<app>
- helm
  - <app>
    - helm
      - templates
        - ...
      - values.yaml # custom config values you provide
      - default-values.yaml # configuration we generate w/ `plural build`
- terraform
  - <submodule>
    - ...
  - main.tf # can override submodule configuration here, see that file for more info
```

In general we expect you to customize the repo as you wish, and any standard terraform or helm command should work as expected.  So if you, say, wanted to switch the database for an app to RDS, you can use the app's terraform folder to define the db and inject its secret into k8s right there.

There are two other metadata files you should be aware of as well:

* `context.yaml` - this is the initial app configuration you provide when running `plural bundle install`.  We use this to generate the resources that are present in your app folder above
* `workspace.yaml` - high level workspace metadata information about your workspace, like the cloud account, cluster name, dns domain, etc

## Secret Management

We automatically encrypt this repo using a go reimplimentation of (git-crypt)[https://github.com/AGWA/git-crypt].  You'll be able to see the AES key you're currently using with `plural crypto export`.  We also drop the key fingerprint into the repo at `.keyid` to validate and provide appropriate error messages.

It's strongly recommended you backup this key, and we provide a backup api for you with `plural crypto backups create`.  You can sync any backup with `plural crypto backups resource NAME`


## Destroying Your Cluster

You can tear down the cluster at any time using `plural destroy`.  If it gets jammed on a specific app, you can simply rerun with `plural destroy --from APP`.

In break glass scenarios where destroy doesn't want to behave, the bulk of the cloud resources are in the bootstrap app, and you can simply run `cd bootstrap && terraform destroy` manually.  If you encounter this feel free to let us know so we can improve our packaging

## Support

If you have any issues with plural or any of the apps in the catalog, we pride ourselves on providing prompt support.  You can join our discord at https://discord.com/invite/pluralsh and we'll try to troubleshoot the issue for you.