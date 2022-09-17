# How to use Develop Jekyll (and GitHub Pages) locally using Docker containers
If you want to create a simple website using Jekyll or GitHub Pages, the typical route you take is to install Ruby and Jekyll on your computer.
There are so many variables in getting it right, that many poeple give up in frustration. Using a Docker container is such an easier way to go.

This repo supports my [YouTube video](https://youtu.be/owHfKAbJ6_M) that shows how to set up a Docker container on your computer so you do not have to install Ruby and Jekyll locally on your own computer.


- [How to use Develop Jekyll (and GitHub Pages) locally using Docker containers](#how-to-use-develop-jekyll-and-github-pages-locally-using-docker-containers)
  - [Benefits of using Docker with Visual Studio Code remote containers](#benefits-of-using-docker-with-visual-studio-code-remote-containers)
  - [Prepare your desktop](#prepare-your-desktop)
  - [Reference links](#reference-links)
  - [To create a new GitHub Pages website from scratch](#to-create-a-new-github-pages-website-from-scratch)
    - [STEP 1: Create a GitHub Pages repo](#step-1-create-a-github-pages-repo)
    - [STEP 2: Enable GitHub Pages](#step-2-enable-github-pages)
    - [STEP 3: Install Visual Studio Code extensions](#step-3-install-visual-studio-code-extensions)
    - [STEP 4: Clone the GitHub repo to your local computer](#step-4-clone-the-github-repo-to-your-local-computer)
    - [STEP 5: Determine Jekyll dependencies](#step-5-determine-jekyll-dependencies)
    - [STEP 6: Determine your docker image](#step-6-determine-your-docker-image)
    - [STEP 6: Create a dockerfile and a Docker container](#step-6-create-a-dockerfile-and-a-docker-container)
    - [STEP 7: Build the Jekyll website](#step-7-build-the-jekyll-website)
    - [STEP 8: Run the Jekyll website](#step-8-run-the-jekyll-website)
    - [STEP 9: Prepare your code for GitHub Pages](#step-9-prepare-your-code-for-github-pages)
    - [STEP 10: Update the .gitignore file (optional)](#step-10-update-the-gitignore-file-optional)
  - [Closing the container](#closing-the-container)
  - [Opening the container](#opening-the-container)
  - [FAQs](#faqs)
    - [Q: What if I delete the container?](#q-what-if-i-delete-the-container)
    - [Q: What if I clone this or some other repo to my computer?](#q-what-if-i-clone-this-or-some-other-repo-to-my-computer)
    - [Q: What if I want to automate the execution of commands once a container is created?](#q-what-if-i-want-to-automate-the-execution-of-commands-once-a-container-is-created)

## Benefits of using Docker with Visual Studio Code remote containers
* Your code lives on your computer, but the website you are testing is built inside a container
* You can test your work locally using any browser on your computer
* No need to install Ruby on your desktop
* No need to install Jekyll on your desktop
* Switch versions of Ruby or Jekyll at any time

## Prepare your desktop
You will need the following accounts or apps on your computer before getting started

**STEP 1: Sign up for Docker and download Docker Desktop**

[Docker.com](https://docker.com)

**STEP 2: Sign up for GitHub for free**

[GitHub.com](https://github.com)

Download Visual Studio Code for free

[Code.visualstudio.com](https://code.visualstudio.com)

## Reference links
If you ever have questions about using Jekyll or finding out dependencies, I provide these helpful links here.

**Bill’s Jekyll and GitHub Pages YouTube playlist**

[YouTube.com playlist](https://www.youtube.com/playlist?list=PLWzwUIYZpnJuT0sH4BN56P5oWTdHJiTNq)

**GitHub Pages dependencies**

[Pages.github.com](https://pages.github.com/versions)

**Jekyll dependencies**

[Rubygems.com](https://rubygems.org/gems/jekyll)

**Docker Hub**

[Docker.copm](https://hub.docker.com)

**devcontainer.json reference documentation**

[Code.visualstudio.com remote container docs](https://code.visualstudio.com/docs/remote/devcontainerjson-reference)



## To create a new GitHub Pages website from scratch

### STEP 1: Create a GitHub Pages repo
1. Create a new GitHub repo with the following options:

| option | Setting |
| :---   | :---    |
| **Name**  | my-jekyll-docker-website _(or whatever you would like to name it)_ |
| **Description**  | Testing Jekyll development using Docker |
| **Add a README file** | Yes |
| **Add a .gitignore file** | Yes. Also, select the Jekyll template |

 
### STEP 2: Enable GitHub Pages
1. Go to your new repo and go to settings
2. Select the Pages menu

| option | Setting |
| :---   | :---    |
| **Source**  | main (branch) |
| **Theme**  | none |

1. You will be provided with a GitHub Pages url link. Copy that to the clipboard
2. Return to your repo, click the gear icon and then paste the url link into the Website field

### STEP 3: Install Visual Studio Code extensions
1. Add the Docker extension from Microsoft
2. Add the Remote - Container extension from Microsoft
3. Restart Visual Studio Code

### STEP 4: Clone the GitHub repo to your local computer
1. In Visual Studio Code, type COMMAND+SHIFT+P, then git clone
2. Type or paste the url for the GitHub pages repo you created
3. Select a folder
4. Open the cloned repo

### STEP 5: Determine Jekyll dependencies
**Option 1 (latest version of Jekyll)**: If you want to use Jekyll and do not care about GitHub Pages, go to the [Jekyll Docs website](https://jekyllrb.com/docs) and find the minimum version of Ruby. The latest version of Jekyll is always on the top-right corner of the site. Here are my recommended settings for the latest version of Jekyll:

| Option | Setting |
| :---   | :---    |
| **Jekyll 4.x.x**  | Ruby 3.0.3 (maybe greater) |
| **Note**  | Use this option if you plan to build your site with a third-party host or use GitHub Actions to build your site |


**Option 2 (GitHub Pages)**: If you want to create a Jekyll site using GitHub Pages, GitHub only supports certain versions. To learn what version GitHub supports and the minimum Ruby version, check out the [GitHub Pages dependency versions page](https://pages.github.com/versions/). As of this writing (April 1, 2022), I recommend the following:


| option | Setting |
| :---   | :---    |
| **Jekyll 3.9.x**  | Ruby 2.7.x or greater, but not 3.x |


### STEP 6: Determine your docker image
Most Jekyll docker images are designed for x86-64 chipsets. That means if you are using an ARM chipset (like Apple Silicon or Windows on ARM), it will (a) not run or (b) run very slowly. Also, many of these images may contain dependencies you do not want or too many dependencies.

I recommend you start with a small and portable Ruby image based on Alpine. If you decide to use something else, then my instructions may not work, so you might want to try my recommendation first and then go on your own after that.

1. Login to [Docker Hub](https://hub.docker.com)
2. Locate the official [Ruby Docker image](https://hub.docker.com/_/ruby)
3. Determine the image you want to use. As of this writing, I recommend:

| option | Setting |
| :---   | :---    |
| **Jekyll 3.9.x / GitHub Pages**  | 2.7.5-alpine3.15 (or just 2.7-alpine) |
| **Jekyll 4.x.x**  | 3.0.3-alpine3.15 (or just 3.0.3-alpine) |


### STEP 6: Create a dockerfile and a Docker container
1. Add the following fcode to your `dockerfile`, being sure to uncomment *one* of the `FROM` lines:
```
# Create a Jekyll container from a Ruby Alpine image

# At a minimum, use Ruby 2.5 or later
# Uncomment the following line if you want to use GitHub Pages (Jekyll 3.9.x):
# FROM ruby:2.7-alpine3.15
# Uncomment the following line if you want to use the latest version of Jekyll:
# FROM ruby:3.0.3-alpine3.15

# Add Jekyll dependencies to Alpine
RUN apk update
RUN apk add --no-cache build-base gcc cmake git

# Update the Ruby bundler and install Jekyll
RUN gem update bundler && gem install bundler jekyll
```

2. Save the dockerfile
3. Type `SHIFT+COMMAND+P`, type `commit all`
4. Type `Create dockerfile` and press `return`
5. Type `SHIFT+COMMAND+P`, type `open folder in container` and press `return`
6. Select the `folder` containing your cloned repo
7. Select the `From dockerfile` option
8. VSC will build the container. This may take a long time for the first time
9. Optionally, you can update the `devcontainer.json` file, but I do not cover those instructions here. To learn more, go to the [Remote container docs](https://code.visualstudio.com/docs/remote/containers) website 

### STEP 7: Build the Jekyll website
1. In Visual Studio Code, open the terminal window and type the following commands:
```
bundle init

# --- Decision time!
# Either install Jekyll 3.9.x for for official GitHub Pages support
bundle add jekyll --version "~>3.9.0"

# Or install the latest version of Jekyll 4.x
bundle add jekyll --version "~>4"
# ---

bundle install
bundle update
bundle exec jekyll new --force --skip-bundle .
bundle add webrick
bundle install
bundle update
```

### STEP 8: Run the Jekyll website
1. In Visual Studio Code, open the terminal window and type:

```
bundle exec jekyll serve --livereload
```

2. Select the `Open in Browser` option and verify the site is running (note: Visual Studio Code has a built-in browser and it may or may not work, so use your desktop browser for best results)
3. After you verify Jekyll is running, go to the terminal in Visual Studio Code and type `CONTROL+C` to stop Jekyll

### STEP 9: Prepare your code for GitHub Pages

**Note**: Even if you are not using GitHub Pages, you still need to set the `url` and `baseurl`. I only cover how to do that for GitHub Pages here

1. Go to your GitHub Pages repo and copy the link for your GitHub Pages website (not the repo, but the website link)
2. In Visual Studio Code, with the repo open and edit the _config.yml file and modify the `baseurl` and `url` fields.

In this example, I will use the GitHub Pages url from this repo, which looks like this:
`https://billraymond.github.io/my-jeckyll-docker-website/`
```
baseurl example: “/my-jeckyll-docker-website”
url: “https://billraymond.github.io”
```
Notice that I removed the trailing `/` at the end of the original url as that is not required

3. Save the `_config.yml` file and close it
4. Type `COMMAND+SHIFT+P`, type `commit all`, and press `return`
5. Type `Create Jekyll site`, and press `return`
6. Type `COMMAND+SHIFT+P`, type `Git Push`, and press `return`
7. Go back to your GitHub Pages repo and refresh the page to make sure the files on your desktop now exist on GitHub
8. After 30 seconds to 5 minutes, go to the GitHub Pages website url and verify the new Jekyll website is running

### STEP 10: Update the .gitignore file (optional)
I did not cover this in the original video, but you may want to update your `.gitignore` file with more options.

**Note**: You probably have some of these entries already. Also, the `.DS_Store` option is for macOS, but it will not matter if you include it or not on another OS.

1. In Visual Studio Code, select the current `.gitignore` file and paste in the following code:
```
_site/
.sass-cache/
.jekyll-cache/
.jekyll-metadata
**/.DS_Store
.bundle/
vendor/
```

Technically, you are done now. However, I will share you some additional steps to learn how to work with the container.

## Closing the container
It is a good practice to close the container when you are done coding as it will free up memomry and resources on your base operating system. You can do that directly inside Visual Studio Code. 
1. To close and exit the container, in Visual Studio Code, close it by typing `COMMAND+SHIFT+P`, then type `Close remote connection`, and press `return`

## Opening the container
If you are familiar with Visual Studio Code, you probably go to the file menu and open a folder. With containers, it is almost the same, but with slightly different steps.

1. To open and start the container, run Visual Studio Code, type `COMMAND+SHIFT+P`, type `Open Folder in container…`, and then press `return`
2. Select the folder containing your Jekyll/GitHub Pages code that contains a `dockerfile` and press `return`
3. If the container already exists on your computer, it should start immediately. If it does not exist, Visual Studio Code will create a new Docker image and container (and possibly a Docker volume)
4. Begin developing!
5. Don't forget you can see your changes in real time by running Jekyll with the following command in the Visual Studio Code terminal window:

```
bundle exec jekyll serve --livereload
```

## FAQs
### Q: What if I delete the container?
A: Good news! The code you work with is always on your local computer, not the container. Follow the steps in this page under the section titled **Opening the container**. However, for the first time, before you can run `jekyll serve`, you must run the following commands:

```
bundle install
bundle update
```

### Q: What if I clone this or some other repo to my computer?
Not a problem. Clone the code to your local computer, then follow the same steps I share in the FAQ titled **What if I delete the container**

**Important**: You will have to modify the `_config.yml` file and update it with your new `baseurl` and `url`, as mentioned in `STEP 9` earlier in this README

### Q: What if I want to automate the execution of commands once a container is created?
A: So as mentioned in the FAQ titled **What if I delete the container**, the `bundle install` and `bundle update` commands need to be run each time after the container has been created. But what if we would like to automate this step? Well lucky for us, there is a way!

We just need to make use of the `onCreateCommand` command property.  
If we open up the `.devcontainer\devcontainer.json` file, it should look like this:  

```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.245.2/containers/docker-existing-dockerfile
{
	"name": "Existing Dockerfile",
	// Sets the run context to one level up instead of the .devcontainer folder.
	"context": "..",

	// Update the 'dockerFile' property if you aren't using the standard 'Dockerfile' filename.
	"dockerFile": "../dockerfile"

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	// "forwardPorts": [],

	// Uncomment the next line to run commands after the container is created - for example installing curl.
	// "postCreateCommand": "apt-get update && apt-get install -y curl",

	// Uncomment when using a ptrace-based debugger like C++, Go, and Rust
	// "runArgs": [ "--cap-add=SYS_PTRACE", "--security-opt", "seccomp=unconfined" ],
	// Uncomment to use the Docker CLI from inside the container. See https://aka.ms/vscode-remote/samples/docker-from-docker.
	// "mounts": [ "source=/var/run/docker.sock,target=/var/run/docker.sock,type=bind" ],
	// Uncomment to connect as a non-root user if you've added one. See https://aka.ms/vscode-remote/containers/non-root.
	// "remoteUser": "vscode"
}
```

We need to do the following:
1. Uncomment `// "postCreateCommand": "apt-get update && apt-get install -y curl",` and remove the comma `,` at the end.
2. Replace `"apt-get update && apt-get install -y curl"` with `"bundle install && bundle update"` (we use the `&&` to be able to chain multiple commands together).
3. Add a comma `,` to the end of `"dockerFile": "../dockerfile"`.  

So the file should look like this:
```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.245.2/containers/docker-existing-dockerfile
{
	"name": "Existing Dockerfile",

	// Sets the run context to one level up instead of the .devcontainer folder.
	"context": "..",

	// Update the 'dockerFile' property if you aren't using the standard 'Dockerfile' filename.
	"dockerFile": "../dockerfile",

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	// "forwardPorts": [],

	// Uncomment the next line to run commands after the container is created - for example installing curl.
	"postCreateCommand": "bundle install && bundle update"

	// Uncomment when using a ptrace-based debugger like C++, Go, and Rust
	// "runArgs": [ "--cap-add=SYS_PTRACE", "--security-opt", "seccomp=unconfined" ],

	// Uncomment to use the Docker CLI from inside the container. See https://aka.ms/vscode-remote/samples/docker-from-docker.
	// "mounts": [ "source=/var/run/docker.sock,target=/var/run/docker.sock,type=bind" ],

	// Uncomment to connect as a non-root user if you've added one. See https://aka.ms/vscode-remote/containers/non-root.
	// "remoteUser": "vscode"
}
```

Now save the file, and try deleting and recreating the container, after the container has been created it should automatically start running the commands we just added. They should be visible within the terminal.  

More information about this and other command properties can be found [here](https://containers.dev/implementors/json_reference/#lifecycle-scripts).
