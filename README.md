# base-dev-setup

An effort to make it easy for any developer to backup, restore, install, update tools you use daily in pretty much any machine (might require some monkey patching though) you ever use and primarily easy to customize and fit your needs.

I picked up Ansible to allow me easily:
- :rocket: executing a playbook;
- :coffee: getting a cup of coffee; and
- :computer: have my computer ready to kick in some great work

So far, it was just tested in `Ubuntu 18.04` to `Ubuntu 19.10`.

## Getting started

If you don't have `ansible` and `awscli` installed you can run [bin/bootstrap](./bin/bootstrap).


### #1. Backup your data

This playbook saves **specified files** to an **S3 bucket** of your choice.

First, you should back up the files that you want to reuse, so create `vars/backup.yml` from [example](./vars/backup.example.yml) and adjust it to your needs.

Finally, execute:

```bash
bin/save-backup
```

Done


### #2. Restoring your data

For restoring, besides downloading the files you've set in the previous step, it also:
- Installs packages and binaries you use frequently; and
- Clones GitHub repositories you're currently working on.

Again, before kicking off, create and adjust the following vars:

- `vars/packages.yml` from [example](./vars/packages.example.yml)
- `vars/repositories.yml` from [example](./vars/repositories.example.yml)

Note that:
- For `packages` you can easily remove or filter tools you might not want at all
- For `repositories` I suggest keeping separate folders for your personal and company repositories.
- Still, for `repositories` I added both folders to your Nautilus bookmarks for quick access


If you're in a new machine, you'll need at least `ansible` and `awscli`. Feel free to run `bin/bootstrap` first.

Now just running:

```bash
bin/restore-backup
```

will do the job.

---

## What it does installs

A quick look [here](roles/packages/tasks/main.yml) would show you what's being installed, but I can summarize as:

- General base tools and requirements (e.g. `curl`, `virtualenv`, `jq`, etc)
- Some **python linters/tools**
- Some **MUST dev tools** (e.g. git, docker, vscodium, etc)
- Some **database tools** (e.g. mysqlcli, dbeaver)
- Some **daily tools** (e.g. slack, zoom, spotify, etc)
- Some **devops tools** (e.g. terraform, kubectl, etc)
- Some **productivity tools** (e.g. wtfutil, moo.do, tusk)

### Customizing it

This playbook was designed to behave like an easy plug-and-play tool.

Do you want to remove something from there? Let's say you're not interested in installing `tilda`, it would be as easy as deleting two lines:

```yml
- import_tasks: tools/tilda.yml
  tags: tilda, binaries, tools
```

The same applies if you want to add something new: Create a small set of tasks and import it.

```yml
- import_tasks: mycustom_tools/my_great_tool.yml
  tags: custom, tools
```

### Updating tools

You can easily update any tool, without having to:
- :sleeping: Search in google the download page
- :sleeping: Head out to the download page
- :sleeping: Download it
- :sleeping: Change chmod or execute it

**Instead of doing any manual, tedious and repetitive work, use this restore playbook.**

Let's say you want to update all dev tools:

`ansible-playbook playbooks/restore.yml --tags dev`

Or maybe you're willing to update only zoom to the latest version. You could simply:

`ansible-playbook playbooks/restore.yml --tags zoom`

Whether you want to update all dev tools except git and docker?

`ansible-playbook playbooks/restore.yml --tags dev --skip-tags git,docker`


## No manual steps at all?

Well, I still have to set up some stuff manually.

Right now, personally, after restoring a new machine I gotta:

- Personalize my dock and favorites
- Enable night light
- Set up my multi-monitor settings
- Set up my keybindings
- Disable [gnome emoji-picker](https://askubuntu.com/questions/1046418/how-do-i-disable-emoji-input-in-ubuntu-mate-18-04)
- Trigger my [VSCodium settings sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)
- Review permissions of some restored files

Always we should strive for automating everything to get working even faster. Pull Requests are welcome!
