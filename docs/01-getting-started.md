---
sidebar_position: 2
---

# Getting Started

### Creating Your First Elemental App

Unlike other frameworks, Elemental doesn't rely on Composer or external libraries. It's as simple as cloning the repository and getting started with good ol' PHP installed on your system.

#### Cloning the Repository

Open your terminal and execute the following command:

```bash
git clone https://github.com/aneesmuzzafer/elemental.git
```

No worries about package managers or dependencies – Elemental is built from scratch to free you from such concerns.

#### Alternatively, via Composer

For those who prefer the Composer route, creating a new Elemental app is just a command away:

```bash
composer create-project fragment/elemental sample-app
```

This will generate a project with a `composer.json` file.

Once your project is ready, kickstart the Elemental local development server with the `ignite` command using our command line engine, *Candle*:

```bash
cd sample-app

php candle ignite
```

Voila! Your application is now accessible at [http://127.0.0.1:8000](http://127.0.0.1:8000/).

We've taken care of the basic setup so you can focus on the magic.

> **Let the enchantment begin!**
