# Documentation Repository for Application Service Adapter

## Branches
| Branch | Usage                            | Sites                                                                                                                                                                                                                                                                                                       |
|--------|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| main   | Branch for the upcoming release. | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/1.0/tas-adapter/GUID-overview.html)                                                                                                                                                          |
| 0-10   | Branch for the v0.10 release.    | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.10/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.10/tas-adapter/GUID-overview.html)       |
| 0-9    | Branch for the v0.9 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.9/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.9/tas-adapter/GUID-overview.html)         |
| 0-8    | Branch for the v0.8 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.8/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.8/tas-adapter/GUID-overview.html)         |
| 0-7    | Branch for the v0.7 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.7/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.7/tas-adapter/GUID-overview.html)         |
| 0-6    | Branch for the v0.6 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.6/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.6/tas-adapter/GUID-overview.html)         |
| 0-5    | Branch for the v0.5 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.5/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.5/tas-adapter/GUID-overview.html)         |
| 0-4    | Branch for the v0.4 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.4/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.4/tas-adapter/GUID-overview.html)         |
| 0-3    | Branch for the v0.3 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.3/tas-adapter/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.3/tas-adapter/GUID-overview.html)         |
| 0-2    | Branch for the v0.2 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.2/tas-adapter-0-2/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.2/tas-adapter-0-2/GUID-overview.html) |
| 0-1    | Branch for the v0.1 release.     | [Staging](https://docs-staging.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.1/tas-adapter-0-1/GUID-overview.html) &middot; [Prod](https://docs.vmware.com/en/Application-Service-Adapter-for-VMware-Tanzu-Application-Platform/0.1/tas-adapter-0-1/GUID-overview.html) |

## Publishing Docs

Staging and Publishing:  

- [DocWorks](https://docworks.vmware.com/) is the main tool for managing docs used by writers.
- [Docs Dashboard](https://docsdash.vmware.com/) is a deployment UI which manages the promotion from staging to pre-prod to production.

The process below describes how to upload our docs to staging, replacing the publication with the same version.

### Prepare Markdown Files
- Markdown files live in this repo.
- Each page requires an entry in [toc.md](docs/toc.md) for the table of contents.
- Images should live in an `images` directory at the same level and linked with a relative link.

### Create the Zip File

Before you generate the zip file for upload, make sure that your repository is up to date with the latest changes. We recommend running `git pull` right beforehand to ensure that any remote changes are pulled to your local repository.

From the root directory of this repository, run the `prepare-zip` script. This script will delete any existing zip files and create a new zip file named `tas-adapter.zip` containing the documentation files.


### Upload the Zip File to Docworks

- Log into [DocWorks](https://docworks.vmware.com/) and navigate to the [Markdown HTML publishing page](https://docworks.vmware.com/md2docs/publish).
- Fill in the fields exactly as in the [`VMwarePub.yaml` file](VMwarePub.yaml). Repeat this every time, and note that the browser can help to remember form fields.
- Select the zip file to upload from your local system. This should be the `tas-adapter.zip` file generated above.
- Click **Upload**, and when prompted, enter your VMware AD password.
- If you see invalid path errors in the yellow box on the right, there are broken links but the site will still be published.
- If the toc.md is invalid, then the site will not build but there is no indication that something is wrong.

### In Docs Dashboard

1. Wait about 1 minute for processing to complete after uploading.
2. Go to https://docsdash.vmware.com/deployment-stage
   
   There should be an entry with a blue link which says `Documentation` and points to staging.

### Promoting to Pre-Prod and Prod

**Prerequisite** Needs additional privileges - reach out to the docs team in [#tanzu-docs](https://vmware.slack.com/archives/C055V2M0H) to ask a writer to do this step for you.

1. Go to Staging publications in docsdash  
  https://docsdash.vmware.com/deployment-stage

2. Select a publication (make sure it's the latest version)

3. Click "Deploy selected to Pre-Prod" and wait for the pop to turn green (refresh if necessary after about 10s)

4. Go to Pre-Prod list  
  https://docsdash.vmware.com/deployment-pre-prod

5. Select a publication

6. Click "Sign off for Release"

7. Wait for your username to show up in the "Signed off by" column

8. Select the publication again

9. Click "Deploy selected to Prod"

### Landing Page and Publications

General information about landing pages:

- Every product has a landing page (Not exactly true: every umbrella product, such as Tanzu Application Platform should have a landing page.)
- The landing page is a container for all the "publications" for a product. Our first publication will be our combined docs for our first Beta, including installation, and user docs.
- Typically there will be a new docs publication for each minor release but not each point release. This version number become part of the URL e.g. Our first release was version `0.1` (see form section above for the current release).
- Some products like [Tanzu Kubernetes Grid](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/index.html) publish separate release notes publications for each point release.
- For comparison see https://docs.vmware.com/en/VMware-Tanzu-Application-Catalog/index.html

