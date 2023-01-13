
<h1 align="center">Hi 👋, I'm Sakshyam Banjade</h1>
<p align="center">सक्षम नाम काफी छ</p>
<h3 align="center">A passionate frontend developer.</h3>

<p align="left"> <img src="https://lh3.googleusercontent.com/pw/AL9nZEWIXVoMKQ0jAYa0bODtRwtBgbx3F7yKWR9aogNFf5aTY3-r2zU1l_FaN7OpWzSE9cSirUBJno5vyPUaj3JcFKh2gIWcGjwJeCGbKoXbCLzOdBRrX8Ss49MLrBJezeuIBK6e1mxzFVFLhGqEU2bTdII=w958-h537-no" /> </p>

Skip to content
DEV Community 👩‍💻👨‍💻
Search...

Log in
Create account

72
Like
27
Jump to Comments
69
Save

Cover image for How to enable GitHub Actions on your Profile README for a snake-eating contribution graph 🐍
Michelle Mannering
Michelle Mannering
Posted on 28 Jun 2021 • Updated on 24 Feb 2022

How to enable GitHub Actions on your Profile README for a snake-eating contribution graph 🐍
#
github
#
markdown
#
opensource
#
tutorial
Git Good (9 Part Series)
1
Using the GitHub CLI to update your repo
2
How to create and pin a gist on GitHub
...
5 more parts...
8
How to use GitHub Pages to host your website, even with multiple repos
9
How to create a Profile README for your Organisation
Lots of people have been talking about and sharing their GitHub Profile READMEs. It was a project we launched last year and developers are loving it. As this feature became more popular, more developers were building templates, plugins, and apps for you to use on your profile.

This post from Supritha caught my attention. I like the way they talk about various pieces of code to have on your GitHub Profile README.

supritha 
How to have an awesome GitHub profile ?
Supritha Ravishankar ・ Jun 8 '21 ・ 5 min read
#github #markdown #opensource #git
One of the comments from Guillaume Falourd pointed to a really cool project from Platane.

GitHub logo Platane / snk
🟩⬜ Generates a snake game from a github user contributions graph and output a screen capture as animated svg or gif
snk
GitHub marketplace type definitions code style

Generates a snake game from a github user contributions graph



Pull a github user's contribution graph Make it a snake Game, generate a snake path where the cells get eaten in an orderly fashion.

Generate a gif or svg image.

Available as github action. Automatically generate a new image at the end of the day. Which makes for great github profile readme

Usage
github action

- uses: Platane/snk@master
  with:
    # github user name to read the contribution graph from (**required**)
    # using action context var `github.repository_owner` or specified user
    github_user_name: ${{ github.repository_owner }}

    # path of the generated gif file
    # If left empty, the gif file will not be generated
    gif_out_path: dist/github-snake.gif

    # path of the generated svg file
    # If left empty, the svg file will not be generated
    svg_out_path: dist/github-snake.svg
example with cron job

interactive demo


platane.github.io/snk

local

npm install
npm
…
View on GitHub
It uses GitHub Actions to build and update a user's contribution graph, and then has a snake eat all your contributions. The output generates a gif file, that you can then show on your GitHub Profile README. I thought this was pretty cool, so I set about adding this to my profile.

Step 1. Setting up GitHub Actions
The first thing you want to ensure before you start this project, is that you have GitHub Actions setup. Head to your Profile and ensure the "Actions" tab is available. Everyone should now have this feature.

Actions Button

Next, head to Platane's Action (available on the Marketplace) and make a copy of the code. You'll need to add this snippet into your Actions file.

Step 2. Creating your GitHub Actions yaml file
This is definitely the part where I got stuck. When looking at the code, I wasn't sure exactly where to add the lines of code mentioned on Marketplace.

After looking at the way Platane had their Actions file setup, I was able to generate the code (with a little help from Bdougie of course).

I've added the full code snippet below and added plenty of comments to (hopefully make it easy to understand).

You can copy this code straight into a blank *.yml file. Make sure you create a new *.yml file under the following directory:

YOUR_GITHUB_USERNAME --> .github --> workflows --> FILE_NAME.yml

# GitHub Action for generating a contribution graph with a snake eating your contributions.

name: Generate Snake

# Controls when the action will run. This action runs every 6 hours.

on:
  schedule:
      # every 6 hours
    - cron: "0 */6 * * *"

# This command allows us to run the Action automatically from the Actions tab.
  workflow_dispatch:

# The sequence of runs in this workflow:
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:

    # Checks repo under $GITHUB_WORKSHOP, so your job can access it
      - uses: actions/checkout@v2

    # Generates the snake  
      - uses: Platane/snk@master
        id: snake-gif
        with:
          github_user_name: mishmanners
          # these next 2 lines generate the files on a branch called "output". This keeps the main branch from cluttering up.
          gif_out_path: dist/github-contribution-grid-snake.gif
          svg_out_path: dist/github-contribution-grid-snake.svg

     # show the status of the build. Makes it easier for debugging (if there's any issues).
      - run: git status

      # Push the changes
      - name: Push changes
        uses: ad-m/github-push-action@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: master
          force: true

      - uses: crazy-max/ghaction-github-pages@v2.1.3
        with:
          # the output branch we mentioned above
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
Step 3. Running GitHub Actions
Once you've added the code above and committed it, head to your Actions tab. Under "Workflows", you should see the "Generate Snake" Action you created above.

Workflow

Click "Run Workflow" and watch your workflow run. You can even expand your workflow and see what's happening in real time.

GIFRunning Actions

Once your workflow has finished running, you should have received a green ✅ "build" checkmark. If so, this means your Action is working nicely. If not, you'll have to go through the code and see where the errors are. The "build" status should give you some indication of where your errors lie. You can also compare it with the yaml file on my GitHub Profile README.

Build green

If you have the green "build" ✅ then you should be good to go. Head back to your repo's "Code" directory, and change your branch to "output". Here you'll find the two versions of your contribution graph with the snake eating it: a *.gif and a *.svg. The good thing about these files is the file is rewritten over itself every time the Action runs. Thus, your contribution graph will always be up to date.

GIFCodeOutput

Step 4. Adding a contribution-eating snake to your profile
Now the final set is to add this to your profile. Grab the file location of your *.gif. It should be something like:

https://github.com/YOUR_USERNAME/YOUR_USERNAME/blob/output/github-contribution-grid-snake.gif

Add this to your profile by copying the file location onto a new line in your markdown file:

![snake gif](https://github.com/YOUR_USERNAME/YOUR_USERNAME/blob/output/github-contribution-grid-snake.gif)

Now you should have a really cool looking snake eating your contributions!

GIFSnakeEating
Hopefully this guide was helpful for you and gives you a good base to start building and adding more Actions on your profile. What have you been adding to your GitHub Profile README? Other there other cool Actions I should be looking at?

Git Good (9 Part Series)
1
Using the GitHub CLI to update your repo
2
How to create and pin a gist on GitHub
...
5 more parts...
8
How to use GitHub Pages to host your website, even with multiple repos
9
How to create a Profile README for your Organisation
Top comments (27)

Subscribe
pic
Add to the discussion
 
 
kushaltanna24 profile image
KushalTanna24
•
3 Jun 22

Work fine.! Here is mine - KushalTanna24


2
 likes
Like
Reply
 
 
msoftware profile image
Michael jentsch
•
9 Jul 21

Great tutorial! Works for me.
Here's mine - github.com/msoftware


2
 likes
Like
Reply
 
 
gustavohgmartins profile image
Gustavo Martins
•
3 Sept 21

Awsome! How did you change the background-color?


Like
Reply
 
 
mishmanners profile image
Michelle Mannering 
•
3 Sept 21

I didn't change the background colour. It's a .png/.svg image, meaning the background is transparent. It looks dark on mine because I have dark mode enabled on GitHub. You can enable it by clicking Settings --> Appearance --> choosing your mode.


1
 like
Like
Thread
 
gustavohgmartins profile image
Gustavo Martins
•
3 Sept 21

Thank you! actually I was using the .gif version, so I switched to the .svg one and voilá


2
 likes
Like
Thread
 
mishmanners profile image
Michelle Mannering 
•
3 Sept 21

Awesome! Glad to be of help 😄


1
 like
Like
Reply
 
 
bpolaswar profile image
Bhumesh Polaswar
•
17 Oct 21

Great worked for me. See github.com/bpolaswar
Thanks.


2
 likes
Like
Reply
 
 
hudsonpufferfish profile image
Hudson Nguyen
•
8 Jan

I got error "The process '/usr/bin/git' failed with exit code 128". Even though I tried to generate a new personal access token, it didn't work. Anybody has any solution for this error?


1
 like
Like
Reply
 
 
viveeeeeek profile image
VivekS.
•
3 Oct 21

Awesome!


1
 like
Like
Reply
 
 
adityamangal1 profile image
Aditya Mangal
•
22 Jul 21

Awesome Tutorial! 🐍


1
 like
Like
Reply
 
 
zieldecastro profile image
Josiel de Castro Goudinho
•
15 Sept 21 • Edited on 15 Sept

It does not work for me. Can someone help me?
github.com/zieldecastro
instagram.com/zieldecastro/


1
 like
Like
Reply
 
 
shasank27 profile image
Shasank Periwal
•
8 Aug 21 • Edited on 8 Aug

Great tutorial!
But can anyone help me, its not showing all my contributions
github.com/shasank27/shasank27


1
 like
Like
Reply
 
 
mishmanners profile image
Michelle Mannering 
•
6 Sept 21 • Edited on 6 Sept

Hey Shasank. I can't see it on your profile, but to show all contributions, you need to make sure all contributions are turned on your profile. You can do this in your settings:

github.com/YOUR_PROFILE --> Settings --> Profile

Then scroll down to "Contributions" and check the box that says "Include private contributions on my profile".

Hope that helps.


1
 like
Like
Reply
 
 
phuocantd profile image
An Nguyễn Phước
•
1 Jun 22

How to auto build workflow everyday


1
 like
Like
Reply
 
 
wkylin profile image
wkylin.w
•
1 May 22

Here's mine:
github.com/wkylin


1
 like
Like
Reply
 
 
laurasiiia profile image
Laura
•
3 Jan 22 • Edited on 3 Jan

Hi, i did everything right and even got the "build" status. But no output branch was created... why is that? Can someone help me? github.com/laurasiiia


1
 like
Like
Reply
 
 
mishmanners profile image
Michelle Mannering 
•
4 Jan 22

I assume you're working off this folder: github.com/laurasiiia/laurasiiia/b...

Since you've got a newer repo (mine account was set up a number of years ago), you have the default branch set to "main". You might need to change the code to be "main" instead of "master".

I created a PR for you: github.com/laurasiiia/laurasiiia/p...
(run it and see if that works)... it might not but worth a shot. Feel free to jump in other people 😄


1
 like
Like
Reply
 
 
nirav97120 profile image
Nirav Prajapati
•
16 Dec 22

Awesome My github profile


1
 like
Like
Reply
 
 
wisnuaero profile image
Adida Wisnu Akbara
•
7 Dec 22

here's mine - github.com/wisnuaero


1
 like
Like
Reply
 
 
brayheart profile image
Tyler Bray
•
10 Nov 22

Thanks for making this guide!


1
 like
Like
Reply
 
 
matheusroichman profile image
Matheus Roichman
•
2 Nov 22

Mine is not showing all the contributions :(
github.com/MatheusRoichman/Matheus...


1
 like
Like
Reply
View full discussion (27 comments)
Code of Conduct • Report abuse
Timeless DEV post...

Git Concepts I Wish I Knew Years Ago
The most used technology by developers is not Javascript.

It's not Python or HTML.

It hardly even gets mentioned in interviews or listed as a pre-requisite for jobs.

I'm talking about Git and version control of course.

One does not simply learn git

Read next
igd753 profile image
Configurando um pipeline básico: Jenkins
Ivo Dias - Jan 4

manasjoshi profile image
Hosting PocketBase on a DigitalOcean Droplet
Manas Joshi - Jan 4

shreyvijayvargiya profile image
Top 5 React Native UI Libraries
shrey vijayvargiya - Jan 4

leonardpuettmann profile image
Drastically decrease the size of your Docker application
Leonard Püttmann - Jan 3


Michelle Mannering
Follow
Developer Advocate | Hackathon Queen | International Speaker
LOCATION
Australia
EDUCATION
The University of Melbourne
WORK
Developer Advocate at GitHub
JOINED
29 May 2020
More from Michelle Mannering
Why 'release' a project? How to automate releases
#opensource #productivity #github #tooling
How to get ready for Hacktoberfest 2022
#hacktoberfest #opensource #tutorial
GitHub's non-code features
#github #beginners #productivity #opensource

# GitHub Action for generating a contribution graph with a snake eating your contributions.

name: Generate Snake

# Controls when the action will run. This action runs every 6 hours.

on:
  schedule:
      # every 6 hours
    - cron: "0 */6 * * *"

# This command allows us to run the Action automatically from the Actions tab.
  workflow_dispatch:

# The sequence of runs in this workflow:
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:

    # Checks repo under $GITHUB_WORKSHOP, so your job can access it
      - uses: actions/checkout@v2

    # Generates the snake  
      - uses: Platane/snk@master
        id: snake-gif
        with:
          github_user_name: mishmanners
          # these next 2 lines generate the files on a branch called "output". This keeps the main branch from cluttering up.
          gif_out_path: dist/github-contribution-grid-snake.gif
          svg_out_path: dist/github-contribution-grid-snake.svg

     # show the status of the build. Makes it easier for debugging (if there's any issues).
      - run: git status

      # Push the changes
      - name: Push changes
        uses: ad-m/github-push-action@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: master
          force: true

      - uses: crazy-max/ghaction-github-pages@v2.1.3
        with:
          # the output branch we mentioned above
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
DEV Community 👩‍💻👨‍💻 — A constructive and inclusive social network for software developers. With you every step of your journey.

Home
Listings
Podcasts
Videos
Tags
FAQ
Forem Shop
Sponsors
About
Contact
Guides
Software comparisons
Code of Conduct
Privacy Policy
Terms of use
Built on Forem — the open source software that powers DEV and other inclusive communities.

Made with love and Ruby on Rails. DEV Community 👩‍💻👨‍💻 © 2016 - 2023.

<h3 align="left">Tools and Languages I Use:</h3>
<p align="left"> 
<a href="https://www.w3.org/html/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg" alt="html5" width="40" height="40"/> </a>  <a href="https://www.w3schools.com/css/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg" alt="css3" width="40" height="40"/> </a><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/javascript/javascript-original.svg" alt="javascript" width="40" height="40"/> </a> <a href="https://www.mongodb.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mongodb/mongodb-original-wordmark.svg" alt="mongodb" width="40" height="40"/> </a>  <a href="https://expressjs.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/express/express-original-wordmark.svg" alt="express" width="40" height="40"/> </a><a href="https://reactjs.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original-wordmark.svg" alt="react" width="40" height="40"/> </a><a href="https://nodejs.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nodejs/nodejs-original-wordmark.svg" alt="nodejs" width="40" height="40"/> </a> <a href="https://getbootstrap.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/bootstrap/bootstrap-plain-wordmark.svg" alt="bootstrap" width="40" height="40"/> </a><a href="https://www.figma.com/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/figma/figma-icon.svg" alt="figma" width="40" height="40"/> </a> 
 

  
 

  

  

  

  

  
  

  

  



</p>
<p align="left"> <a href="https://github.com/ryo-ma/github-profile-trophy"><img src="https://github-profile-trophy.vercel.app/?username=banjadesakshyam" alt="banjadesakshyam" /></a> </p>



- 💬 Ask me about **front-end web development**

- 📫 How to reach me **sakshyambanjade@gmail.com**

- Intrested in Frontend Intership [full time]



          
<h3 align="left">Connect with me:</h3>
<p align="left">
<a href="https://twitter.com/sakshyambanjade" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/twitter.svg" alt="sakshyambanjade" height="30" width="40" /></a>
<a href="https://linkedin.com/in/sakshyam banjade" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="sakshyam banjade" height="30" width="40" /></a>
<a href="https://stackoverflow.com/users/sakshyam banjade" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/stack-overflow.svg" alt="sakshyam banjade" height="30" width="40" /></a>
<a href="https://www.leetcode.com/sakshyambanjade" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/leet-code.svg" alt="sakshyambanjade" height="30" width="40" /></a>
</p>
<!-- <p><img align="center" src="https://github-readme-streak-stats.herokuapp.com/?user=banjadesakshyam&" alt="banjadesakshyam" /></p> -->




<!-- <p>&nbsp;<img align="center" src="https://github-readme-stats.vercel.app/api?username=banjadesakshyam&show_icons=true&locale=en" alt="banjadesakshyam" /></p> -->


<!-- <p><img align="left" src="https://github-readme-stats.vercel.app/api/top-langs?username=banjadesakshyam&show_icons=true&locale=en&layout=compact" alt="banjadesakshyam" /></p>
 -->
 
 
 
 
 
 
 
 
 
 </br>
 
<h3 align="left">Proudly :</h3> 
 <p align="left"> <img src="https://lh3.googleusercontent.com/epoYM_4xpYYx4E4BlQenqKcr0VnLyeNffl-KUoDix0kaPeNhZ4N2Ht8asXwjO6QVmqosLY47yw1WdVeuBrdB3ZGc4lohO2e9bYse-yv9McdCwn90xa1dPg2pOnvPeKUa2c0CiFnxm12tLz3hmgTfYsoIWjDC6GZ63FfIFFxS23exLG-YeVAyXEU-HBOPVNtOlKIGU0MSgsGA4OPprX8HFLDLg8rdfB8imc_Zj4zbn0k5fZefZlGaTuDv7DwVSL7EIyYRL5PAYJbolKof0vulADDaTFtP--WJG0tBT5lPgzsZ5zIKIyIABGiDK0k4wyt7hxHD02-3OZlWSWY2ZoT7Pquy7Nr-avwXSscKq_VeQs07Rn8Iswhd3tSmJlgjH1EQZe192TcFlq5ZysOXkxYhmjo3SM8F9xxp9zAwpk1aLj1gzTk4xF2P7_gXQ6yLNvnmb0gSCe-BoBgN5sFVa6Ky9Ni4ylaTkS0WkHBR7AwA-04eT42yRv7CLKWQIhewEaGPpq-sqaSmYdeNaseS2BNpvlJggYnMBieQOBUoZwGhIrKT4z0wGLqSzKKbZm7tncGLrwH-0Uaw4JT7m_F4kvGfxjTnsX_zVyMdOrANusF4Vmt5y7oY7hky_B8Je2tNZpqZozNac7A9H-mANWWPWJij5ItCSntPM49fUw-dYuEhGxIeIOb1-TpREVed5ydoZvWK1qkWPhre_B0gEVPl0YgViNcEW1-zFUgKt1mRCeWe_Yyt6lSAdQ9F8y9scc28iJU5dCZdjxV2jQPI2JPmQ_pf5SOPPdJJ8mCqe3cj6VYtpFaGhb5Ixw5px91OHw5WLIttYY8JHaXuIpQgRmkvBsvcY-DlAuzCinPPMBY8PEYpiqQYdz8eRlNuS2PyFYPgWgtvMjoRcaKrJUdfYBQtKEeNgEuh9fE_0gAX7J2UcoRiRqgta9v7beO1OxS0_LezOSZi4KkvxxnosVOpcM1r5P10J5I_DXtiksqEve8Psv04RRb6ZDDQ9rq7f9Zv1A9BJ1W4kCG8f1lR-sx7GLa2ZVaMul_mLITx=w278-h183-no?authuser=0" /> </p>



 
 
 
 
 
 
  
 
 
