# GitHub Tutorial

This tutorial provides an introduction to using `trestlebot` with GitHub.  We will be using a single GitHub repository for our trestle authoring workspace and executing the `trestlebot` commands as GitHub actions.  Note, each repo is intended to support authoring a single OSCAL model type (SSP, component definition, etc.).  If authoring more than one, then a dedeicated repository should be used for each model.


### 1. Prerequisites

Before moving on, please ensure you have completed the following:

1. create a new (or use an existing) empty GitHub repository
2. clone the repo to your local workstation
3. clone the [trestle-bot](https://github.com/RedHatProductSecurity/trestle-bot/tree/main) repo to your local workstation and run `poetry install`


### 2. Set Permissions for GitHub Actions

The `trestlebot` commands will be run inside of GitHub actions.  These commands often perform `write` level operations against the repo contents.  The following repo settings need to be in place to support these actions.

1. Click the `Settings` tab for your GitHub repo 
2. Select `Actions` -> `General` from the left-hand menu
3. Scroll down to `Workflow permissions`
4. Ensure `Read repository contents and packages permissions` is selected
5. Ensure `Allow GitHub Actions to create and approve pull requests` is checked

### 3. Initialize trestlebot Workspace

We will now use the `trestlebot init` command to initialize our emtpy GitHub repository.  Unlike the other trestlebot commands, this command is run on your local workstation.  The trestlebot commands can be installed by cloning the [trestle-bot](https://github.com/RedHatProductSecurity/trestle-bot/tree/main) repo and running `poetry install`.  For this tutorial we will be authoring a component-definition.

```
trestlebot-init --oscal-model compdef --working-dir /path-to-your-repo
```

You should now see the following directories in your repo.


```bash
.
├── catalogs
├── component-definitions
├── markdown 
├── profiles
├── rules
├── .github
├── .trestle
└── .trestlebot
```

You will notice several files within the `.github/workflows` directory.  These are the trestlebot actions that will run as we make changes to the repo contents.

You can now add any catalog or profile content needed for you authoring process.  For this example, we will add the NIST SP 800-53 Rev. 5 catalog to our `/catalogs` directory.

```
mkdir catalogs/nist_rev5_800_53
wget https://raw.githubusercontent.com/usnistgov/oscal-content/release-v1.0.5-update/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json -O catalogs/nist_rev5_800_53/catalog.json
```

Now we will add the NIST SP 800-53 Rev. 5 High Baseline profile to our `profiles/` directory.

```
mkdir profiles/nist_rev5_800_53
wget https://raw.githubusercontent.com/usnistgov/oscal-content/release-v1.0.5-update/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_HIGH-baseline_profile.json -O profiles/nist_rev5_800_53/profile.json
```

Our `profile.json` file contains a reference to our `catalog.json` file.  By default, this path is not resolvable by cmpliance-trestle, so we need to run the following command to update the `href` value in the JSON.

```
sed -i 's/NIST_SP-800-53_rev5_catalog.json/trestle:\/\/catalogs\/nist_rev5_800_53\/catalog.json/g' profiles/nist_rev5_800_53/profile.json
```

Now that we have the initial content needed to begin authoring, go ahead and commit and push to the remote GitHub repo.


### 4. Create a New Component Definition

Now it's time to run our first trestlebot action!  We will go ahead and create our first component definition.

1. Open to your GitHub repo in a web browser.
2. Click to the `Actions` tab from the top menu.
3. Click the `Trestle-bot create component definition` action from the left-hand menu.
4. Click `Run Workflow` which will open up a dialog box.
5. Enter the following values:

* _Name of the Trestle profile to use for the component definition:_ `nist_rev5_800_53`
* _Name of the component definition to create:_ `my-first-compdef`
* _Name of the component to create in the generated component definition:_ `test-component`
* _Type of the component (e.g. service, policy, physical, validation, etc.):_ `service`
* _Description of the component to create:_ `Testing trestlebot init`

6. Click `Run Workflow`

Once the workflow has completed you should have a new Pull Request containing the files trestlebot generated for the component definition.  After reviewing the files you can go ahead and merge the PR!

Congrats, you have sucessfully created a new trestlebot workspace and now have an authoring environment!