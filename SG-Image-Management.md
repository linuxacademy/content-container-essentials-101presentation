Image Management
================

Pulling Images
--------------

- By default, Docker pulls/pushes images from/to Docker Hub
- Private repos are available, and you can set up your own (i.e. DTR)
- Command syntax for pulling an image
  - `docker pull <repository>/<image name>:<tag>`
  - Repository is the public or private repository the image is stored in -- optional
  - Image name is the overall name of the image -- e.g. centos, ubuntu, nginx...
  - Tag is the specific version of the image you want to pull -- e.g. centos:7, ubuntu:16.04
  - If no tag is provided, command defaults to latest version of image
  - Can also pull all images with the same name simultaneously --  `docker pull -a <image>`
- `docker images` shows currently installed images
  - --all shows all images, by default intermediary images are hidden
  - --digests shows digest information
  - --filter allows for filtering by labels or dates
    - --filter "before=centos:6"
- `docker images -q` outputs a list of truncated image IDs -- useful for feeding to another command

Finding Images
--------------

- Basic search: `docker search <keyword>`
  - Search looks through name and description of images on Docker Hub and returns results ordered by number of stars left by users
  - Can filter using --filter as well
    - "stars=<#>" finds images with that number of stars or more
    - "is-official=<true/false>" finds official images
    - "is-automated=<true/false>" finds images that are updated automatically
  - --limit <#> limits to top # results

Tagging Images
--------------

- Lets you add to a currently existing image and keep it separate without making a Dockerfile
- `docker tag <original image>:<original tag> <new image>:<new tag>
- Preserves original file system, just creates a link to it
- Can also include a repository name in the standard format

Managing Images from CLI
----------------

- For a full list of image commands, just run `docker image`
- `docker image history`
  - Displays the history and build process of an image
  - Build process is a series of layered containers, so will show you every step visible to Docker
  - However, will only display changes made locally, prebuilt images usually don't come with a history
- `docker image save`
  - Packs image and necessary layers/data needed to load or build that image into a .tar
  - Will output to stdout unless redirected
  - This changes the image ID, Docker will see it as a different image upon reloading
- Restoring backups from .tars
  - `docker import - <image>:<tag> < <tar>`
    - Not required to give image a name and tag, but none will be automaticlally imported if you don't
  - `docker load < <tar>`
    - Needs to restore from a filestream or an --input argument
    - Will restore name and tag automatically
- Pruning unused images
  - `docker images prune` removes dangling images not associated with containers
  - `docker images prune -a` removes all images not currently associated with a container
  
Inspecting Images
-----------------

- `docker image inspect`
- Provides information a given Docker object (image, container, node...)
- Output is in JSON
- Inspection formats are similar between images and containers
- Can reformat output to just get the information you want with --format <string>
  - "{{.<section>.<attribute>}} provides value for the given attribute key
  - "{{json .<section>}}" will provide key-value pairs in the given section
  - "{{.section}} will pull all values in a given section
  
  
