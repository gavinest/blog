[![Netlify Status](https://api.netlify.com/api/v1/badges/1808a666-01fa-4431-8a66-6fdf995bbe1f/deploy-status)](https://app.netlify.com/sites/splendid-bienenstitch-32cfcc/deploys)
# [gavinest.com](https://www.gavinest.com)

My personal [blog & website](https://wwww.gavinest.com).

## Technologies Used

* [Netlify](https://app.netlify.com/)
* [Zola](https://www.getzola.org/) with theme [appollo-dracula](https://github.com/tontinton/apollo_dracula)

## Local Development

Clone the repo and submodule.

```bash
git clone --recurse-submodules git@github.com:gavinest/blog.git
```

Run `zola serve` to start the local server and navigate to [http://127.0.0.1:1111/](http://127.0.0.1:1111/).

## Deploying

The site is deployed via Netlify. The deployment is triggered automatically when a new commit is pushed to the `master` branch.

Previously, netflify used a different Zola version to build then the one I used locally. This lead to some issues. To fix, I specified the Zola version in the [netlify.toml](netlify.toml).
See the Zola docs on netflify [here](https://www.getzola.org/documentation/deployment/netlify/).
