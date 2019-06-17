# Pantheon Documentation Guide

Below is a "brief" guide that introduces you to Pantheon.

I encourage your developer to read the Pantheon Documentation, as it's rather comprehensive. Pantheon is not a traditional shared web hosting service, so there is a bit of a learning curve.

- [Pantheon Documentation](https://pantheon.io/docs/)
- [Quick Start](https://pantheon.io/docs/guides/quickstart/)
- [Get Started](https://pantheon.io/docs/get-started/)
- [Develop](https://pantheon.io/docs/develop/)
- [Go Live](https://pantheon.io/docs/go-live/)
- [Explore Platform Architecture](https://pantheon.io/docs/platform/)
- [Automate & Integrate](https://pantheon.io/docs/automate/)
- [Quick Guides List](https://pantheon.io/docs/guides/)

## Pantheon Account Setup

In order to access the hosting environment for the website, you will need an account for your develper on our hosting platform: Pantheon.

- We will need to create a developer account for you in order to access the hosting database. If your developer doesn't have one yet, please contact us. We will send an invite.
- You will need to [create a SSH Key](https://pantheon.io/docs/ssh-keys/) if you haven't done so already and add it to your Pantheon account profile in order to connect to the website via Git or SFTP.

Once your account is created, you will have full adminstrative control of the website.

## Pantheon Concepts

Pantheon is unlike a traditional web host in that it isn't a shared hosting platform. It's more akin to what you'd find on Github. Each Pantheon website consists of a `Development`, `Test`, and `Live` branch of the website. Each of these branches has an entirely isolated filesystem and database. So don't think of them as being connected, but rather clones of each other.

The Pantheon workflow is also rather unique, as you are unable to write to new or existing files on the `Test` or `Live` branches of the website. This means that you can't upload plugins or make changes to template or theme files directly on the `Live` site. You'll notice this is reflected in the Wordpress Dashboard, as certain options are removed, such as the `Theme Editor`, or the `Add New Plugin` button. However, once you switch to the `Dev` branch, you'll notice all of these options are back in place where you'd expect them. Wordpress websites on Pantheon have custom plugins running in the background that help accomplish features like this.

### Pantheon Branches

Most of the time, people will be working on the `Live` branch of the website. This is the branch of the website that external users visit, and it is also the branch that all the developer/staff/users log in to in order to add and edit new content. The `Test` and `Dev` branches are used only for your developer. Here are the URLs for the dashboard and branches:

- [Pantheon Dashboard](http://dev-websitename.pantheonsite.io/)
- Dev Branch Exmaple: `dev-websitename.pantheonsite.io`
- Test Branch Exmaple: `test-websitename.pantheonsite.io`
- Live Branch Exmaple: `www.websitename.org`

`websitename` will be replaced with the name of your website, and is used as a placeholder in this documentation.

### Working in the Development Branch

The first and most important thing to understand about Pantheon is that each branch is using its own isolated database and file system. That means that every time you're about to add new plugins or upgrade Wordpress in `Dev`, you need to clone a fresh copy of the database from the `Live` branch of the website to overwrite the existing `Dev` database and files. This is because as users are adding new content or interacting with the `Live` site, the `Dev` database stays frozen in time and falls further and further behind.

In order to do this, make sure you're on the `Dev` tab in the dashboard:

1. Click the `Database / Files` tab on the left hand side.
2. The `From This Environment` dropdown needs to be set to `Live`.
3. Always leave `Clone Database` and `Clone Files` checked. In this case, `Files` refers to the media library of Wordpress, not theme or plugin files.
4. The `From` tab needs to be set to `www.websitename.com` and not `websitename.com` or `live-websitename.pantheonsite.io`. It is important to include that `www.` prefix. This is the most crucial and important step of the entire process. This is because Wordpress has hardcoded URLs in the database, and since you're copying from the `Live` site, those URLs are written as `www.website.com` instead of `dev-websitename.pantheonsite.io`. Essentially this operation scans the entire database for instances of `www.websitename.com` and rewrites them into `dev-websitename.pantheonsite.io`.
5. The `To` tab can still be left alone as it only has one option.
6. The `Convert URLs Protocol to` dropdown should be left as-is (no conversion).
7. Once all the options are set, click `Clone the Database and the Files from Live into the Development Environment`.

This may take 30s-1min to work. If you click the `Workflows` tab near the top right of the screen, you can see progress bars for this operation. Once it is complete, you have the most up-to-date copy of the `Live` site cloned to the `Dev` branch. The sites should look and operate identically if this was done correctly.

At this point, you can upload new plugins to the site via SFTP. Or, you can add them directly through the Plugins tab in the Wordpress dashboard. Once you've added or edited new files, they will show up under the `Code` tab under the `Dev` branch. You will need to make a commit message, and the commit these files.

Or if you prefer, you can clone the site using Git (always run a Git pull before starting new work or adding new files to ensure your local clone has the latest files downloaded). Once you're done, just commit and push the files to master.

### Applying Upstream Updates

Whenever Wordpress has a version update, or Pantheon updates some code for the server (such as the PHP version), these will need to be applied in the `Dev` branch.

The site must be in Git mode in order to apply these updates. We recommend following the steps above to clone the latest database and files from the `Live` site, committing those changes, and then switching the site to `Git` mode and applying those updates.

Once all your file edits are complete and committed, you're ready to move the files and database from `Dev`->`Test`->`Live` and complete the operation.

## Cloning the Branches

A crucial concept to understand is that we need to move the database and files from `Dev`->`Test`->`Live` once we're finished committing our changes to the `Dev` branch.

This is because some plugin or Wordpress updates modify the database. For example: If you've enabled a plugin in `Dev` and then configured it, it stores those configuration changes in the `Dev` database. If you were to only push your _file_ changes from `Dev`->`Test`->`Live`, the `Live` branch would have the new files, but would still be running on its own isolated version of its database. So it wouldn't have any of those configurations in place.

A separate issue here is that any content or changes that have been made on the `Live` branch while you've been working on `Dev` would be overwritten when you clone `Dev`->`Test`->`Live`. This is why we typically add new plugins to the site during off hours late at night or early in the morning when no other users are logged into the site making changes.

### Cloning Dev to Test

Now that you understand these gotchas, you can begin the process of cloning your changes up to `Live`:

1. First, click the `Test` tab at the top.
2. If you committed your files correctly in `Dev`, you should see a yellow deploy box available to deploy under the `Deploys` tab. This deploy is the culmination of all your individual commits from the `Dev` branch.
3. Leave the `Pull files and the database from the Live environment?` box unchecked.
4. Enter a message summarizing the changes you made under the `Deploy Log Message:` box.
5. Once you hit `Deploy Code from Development to Test Environment` all of your code changes will be copied from `Dev` to the `Test` branch.
6. Now, click on the `Database / Files` tab on the left.
7. `From this Environment:` should be set to `Development`.
8. Leave `Clone Database` and `Clone Files` checked.
9. All other settings can be left as-is, except you'll want to check `Clear Caches` at the bottom.
10. Click `Clone the Database & the Files from Development into the Test Environment`.

After following these steps, you've essentially cloned `Dev` into `Test` perfectly. It now is using all the file changes you made in `Dev`, and the database has been copied and converted as well. You can check the `Test` website and ensure your changes worked correctly.

### Cloning Test to Live

Finally, you're ready to do a similar set of steps on the `Live` branch:

1. First, click the `Live` tab at the top.
2. If you've deployed your files correctly in `Test`, you should see a yellow deploy box available under the `Deploys` tab.
3. Enter a message summarizing the changes you made under the `Deploy Log Message:` box.
4. Once you hit `Deploy Code from Test to Live Environment`, all of your code changes will be copied from `Test` to the `Live` branch.
5. Now, click on the `Database / Files` tab on the left.
6. `From this Environment:` should be set to `Development`. Note that we are not using the `Test` database, we just clone from `Dev` again.
7. Leave `Clone Database` and `Clone Files` checked.
8. Leave the `From` box as-is.
9. Make sure to change the `To:` box to `www.websitename.com`. This is an extremely important step. If you forget to do this, all the URLs in the database on the `Live` site will still be `dev-websitename.pantheonsite.io` instead of the correct `www.websitename.com` URLs. It is crucial to include the `www.` prefix.
10. For the `Convert URLs' Protocol to:` box just leave it as-is (no conversion).
11. Check `Clear Caches` at the bottom.
12. Click `Clone the Database & the Files from Development into the Live Environment`.
13. Finally, a warning box will appear. You'll need to type `CLONE` into it for it to work. This is warning you that any changes that have occurred in the Live database will be _overwritten_ by the `Dev` database. This all comes down to timing. You want to add your plugins and updates in the evenings or early mornings before users or staff are modifying the `Live` website. You don't want to work in `Dev` for days or even hours at a time. Make your changes, and copy the database _quickly_ so that you aren't overwriting content or edits that have been made by users while you were busy in `Dev`.
