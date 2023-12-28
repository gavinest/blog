+++
title = "Separate gcloud configs for personal and work projects"
date = "2023-12-28"
[taxonomies]
  tags = ["gcp"]
+++

I use Google Cloud for work and for personal projects. My work is done in one GCP organization that is owned by my work and my personal projects are in another GCP organization that is owned by me. I'd need a strict separation between the two when developing.

Today I'll cover using multiple gcloud configurations to accomplish this with minimal friction.

From the [Google docs](https://cloud.google.com/sdk/docs/configurations#multiple_configurations) multiple configurations can also be useful if you:

* Use multiple projects
* Use multiple authorization accounts
* Perform generally independent tasks in different projects

This article assumes that you have the [GCloud SDK](https://cloud.google.com/sdk/docs/install) installed and set up.

```bash
# 1. list your configurations (the `default` configuration was created when the sdk was set-up)
$ gcloud config configurations list
>>> NAME      IS_ACTIVE  ACCOUNT.        PROJECT               
>>> default   True       gavin@work.com  aworkproject                    

# 2. create the new configuration
$ gcloud config configurations create personal

# 3. list to see the new configuration (note: gcloud set the newly created configuration to active )
$ gcloud config configurations list
>>> NAME      IS_ACTIVE  ACCOUNT            PROJECT               
>>> default   False      gavin@work.com     aworkproject 
>>> personal  True       gavin@personal.com

# 4. set the account property on the configuration
$ gcloud config set account gavin@personal.com

# 5. auth to the account and follow the prompts
$ gcloud auth login

# 6. add additional properties to the configuration as wanted
$ gcloud config set project mypersonalproject

# 7. run a gcloud command to verify the new configuration is working
$ gcloud projects list
```

# Switching between Configurations

```bash
# switch between configurations
$ gcloud config configurations activate work
```

There we have it.

When I am doing work I make sure to activate my work configuration (default in this example) and vice-versa when I am working on personal projects. If I forget gcloud greets me with an error for a reminder.
