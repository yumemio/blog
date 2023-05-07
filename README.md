# yumem.io

## What's this?

This is the source code of my personal blog hosted at [https://yumem.io](https://yumem.io).

The blog is powered by the [Hugo](https://gohugo.io/) static site generator and uses a custom theme based on [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke).

The blog provides tutorials, learning materials and other useful information for AI/ML and GIS engineers.

## How to build the website

1. Install [Docker Engine](https://docs.docker.com/engine/install/).
1. Run the following command to set up the development server. Replace `<IP ADDRESS>` with the IP address of your machine:
    ```console
    $ ./hugo serve -p 4000 -b http://<IP ADDRESS>:4000 --disableFastRender --noHTTPCache --gc
    ```
1. Access `http://localhost:4000` to view the webpage.
1. Run the following command to build static files:
    ```console
    $ ./hugo build
    ```

## Credits

* Hugo: https://gohugo.io/
* Ananke theme: https://github.com/theNewDynamic/gohugo-theme-ananke

## License

### Source code

Licensed under [GPL 2.0](./LICENSE_GPL.md).

### Contents of the website

Files under `content/` directory, which constitute the contents of the website, are licensed under [Creative Commons CC BY-SA 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/).

