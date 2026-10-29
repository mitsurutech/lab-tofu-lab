# OpenTofu lab for classrooms: infrastructure as code with plan, apply and state

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml)

A ready-made **OpenTofu lab** for teaching infrastructure as code, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets an Ubuntu **VM** with **OpenTofu** installed and three folders of Terraform-style files. They write a small project with **variables**, **outputs**, **count** and **for_each**, read a **plan** after a change, look at the **state**, and **fix a broken project**. Nothing is installed on the student's computer, and OpenTofu is free and open source.

## What the student does

The lab leaves three folders in `~/tofu-lab` and a `README.md` in the home folder that says what to do in six parts and gives none of the answers:

1. `hello`: a first `tofu init`, `tofu plan` and `tofu apply`, and the state file left behind.
2. `hello`: add a variable, change the output, and see whether a change is an update or a replacement.
3. `campus`: write a project from nothing. Three variables with defaults, one `terraform_data` resource per server name made with `for_each`, one per room made with `count`, a summary resource that reads both, and two outputs. `tofu fmt` must leave the files as they are.
4. `campus`: change the list of servers and say, from the plan alone, what will be destroyed, created and left alone.
5. `hello`: read the state with `tofu state list`, delete it, and watch the plan want to create everything again.
6. `fix`: a project with three mistakes (a value of the wrong type, a variable that does not exist, two resources that wait for each other for ever), found with `tofu validate`. When it is fixed, `tofu apply -var pods=7` must report 7 pods.

A basic lesson on the platform, OpenTofu Basics, is worked in this lab too.

## How it is marked

The lab has a **check**, a script that marks a handed-in VM without the teacher reading it. It runs on a copy of the saved machine after a fresh start. It copies the student's `campus` and `fix` folders to a new place, so their own state does not count, and refuses any provider or provisioner. It runs `tofu validate` and `tofu fmt -check`. For `campus` it then **chooses values nobody has seen** (a random site name, its own servers and rooms), plans and applies, and checks the state: one server for each name with the site and the name as its input, the rooms numbered from one, an output `summary` that reads a resource rather than being typed in, and the output `server_names`. It removes one server and adds two, plans again, and requires that only the one is destroyed and only the two created. It applies, requires that nothing is left to change, and destroys. Last, `fix` has to validate, apply with `pods=7`, keep its three resources and follow the variable. The first line of what it prints says what is not right.

What it does not do: it never reaches a real cloud, since none can be reached from the lab, and a student with root on their own VM could still tamper with OpenTofu itself. It is a check for a classroom, not a proof.

## What is in the file

`tofu-lab.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs a few tools from Ubuntu's archive, downloads OpenTofu 1.13.2 from its GitHub release, checks it against a pinned SHA-256 and installs it, then writes the three folders, the `tofulab` helper (`sudo tofulab install` and `sudo tofulab reset`) and the README |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the OpenTofu sample, or paste the contents of `tofu-lab.lab.yaml`. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. The first start takes a minute or two while OpenTofu is downloaded. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-tofu-lab), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the five keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else. That is why **no provider can be downloaded and nothing real is ever made**: AWS, Docker and the rest come from a registry the VM cannot reach. The lab uses the one resource built into OpenTofu, `terraform_data`, which only stores a value and hands it back; variables, outputs, `count`, `for_each`, dependencies, plan, apply, destroy and the state are the real thing. The student's README shows a provider's resource block as text to read and the lab never runs it. OpenTofu itself is downloaded from GitHub, which a student's VM reaches only with a free account: a visitor's VM cannot install it, and the check then says OpenTofu is missing.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) and [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). You may share it and adapt it for learning and teaching, if you credit Mitsuru Technologies Ltd, do not use it commercially, and share anything you make from it under the same licence. The full text is in the `LICENSE` file.

[OpenTofu](https://opentofu.org) (MPL-2.0) is downloaded from the project's own GitHub release when a VM starts; nothing of it is distributed here.
