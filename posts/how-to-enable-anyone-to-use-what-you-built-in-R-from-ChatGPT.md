---
title: How to enable anyone to use what you built in R from ChatGPT
layout: default
---

# How to enable anyone to use what you built in R from ChatGPT

---

Click [here](https://github.com/my-little-dev-blog/site/blob/main/posts/how-to-enable-anyone-to-use-what-you-built-in-R-from-ChatGPT.md) to view this post on Github.

---

# Demo

![Demo](https://i.imgur.com/IpdxuZU.png)

# Introduction

## What will you show me in this post?

In this post, I'll walk you through the process of turning a simple R script into a Plumber API, deploying that API as a publicly accessible web service on Google Cloud, and integrating it with ChatGPT as a custom action within a custom GPT.

## What steps will we complete along the way?

1. Write some R code that does something useful
2. Turn your R code into an R function
3. Turn your R function into an API using Plumber
4. Turn your Plumber API into a web service using Google Cloud
5. Turn your web service into a ChatGPT custom Action using a My GPT

## Who is this post for?

Anyone who enjoys building useful things with R that has at least a little familiarity with it. Some familiarity with other technical components may be helpful, but should not be necessary, assuming you are willing to be persistent -- especially because you can use tools like ChatGPT, Claude, Perplexity, etc. to assist you along the way. You may very well see some things here that feel complex and maybe even overwhelming. Stick to your guns. Use the tools. You got this.

## What if I hit a roadblock, or just want to learn more about something along the way?

Use tools like ChatGPT.

For blockers, just say something like, "I am trying to do ..." and then copy / paste the relevant chunk of text from below.  Then say "But I run into ...". Then copy / paste the relevant error messages (or whatever other information -- remember with some of these tools (e.g., Chat) you can even just take a bunch of screenshots and attach them to your message!). Then say "How do I fix it?". Put this all in one big message and send it off. Do what the tool says. And then iterate as needed until you've resolved the issue. If even after that you are still blocked, please see the "Reactions and contributions are welcome!" section at the end of the post for more info about how to best proceed.

For learning, use a similar approach, just substituting a bit of info about what you would like to learn for the error message above. Again, iteration is almost always helpful (and, of course, verification where necessary).

## Why is the post written the way it is?

This post is a wall of text. Yes. I know. Not ideal. However, a) if I couldn't put this together quickly, I wouldn't put it together at all, and it's a lot easier to do it this way than with visuals, videos, etc., b) this makes it especially easy to copy a bit of text from here and drop it into tools like ChatGPT to get the specific guidance you need quickly and easily.

## If I appreciate this post, what might I be able to do to help you out?

Oh I like that question! :)

My goal with this blog is to share learnings as I go in hopes that others might benefit from me doing so. The more feedback you provide to me, the better (note: it's anonymous). I will appreciate it enormously. Please, please click [here](https://forms.gle/ubkuygBSvwTB7G18A) to do, even if you only spend a few seconds providing the least bit of feedback.

Also, I will appreciate if you'll collaboratively work with me to improve this and other posts as I go. Or even submit your own posts to consider adding. Pull requests, issues, and emails are welcome, per the "Reactions and contributions are welcome!" section at the end of the post. If someday we have lots of contributors to this repo -- thus enabling even more people to learn even more from the things we are doing as we go -- that's the holy grail. Anything that others who build software related to what you see here is fair game.

The developer community has given so much to me. I am very delighted to try to return the favor. I would be glad to have your help doing so along the way.

## Why the disclaimers in the following section?

I want to share learnings with you that involve some risks -- especially if not thoughtfully handled. With the disclaimers I am trying to help you understand some of the responsibility you are accepting by following along. As I built what you see here, I felt comfortable, and it only costs me a couple of dollars to do it, because I quickly disabled all cloud resources after having done it. But you should be sure that if you feel uncomfortable you don't proceed. If you mess up (e.g., forget to shut your cloud resources down), it could cost you. I encourage you to do learning along the way to gain comfort. But, again, if you are uncomfortable, don't proceed, and be sure to kill or delete everything.

# Disclaimers

---

<p style="color:red; font-weight:bold;">
DISCLAIMER! The information provided in this guide is for educational purposes only. By following the instructions in this tutorial, you assume all risks associated with its implementation. The author of this guide assumes no responsibility or liability for any errors or omissions in the content of this guide, for any damages or losses that may arise from following the steps outlined herein, or for any costs incurred from following the steps outlined herein.
</p>

<p style="color:red; font-weight:bold;">
SECURITY RISKS! By following the steps in this guide, you will be deploying a publicly accessible service on Google Cloud with minimal security protections. This means that the API you create will be exposed to the internet and potentially vulnerable to unauthorized access, attacks, or exploitation.
</p>

<p style="color:red; font-weight:bold;">
PUBLIC EXPOSURE WARNING! Any data transmitted or handled by this service could be intercepted or manipulated by malicious actors. Sensitive or personal information should never be used with this prototype. Users should be aware that this setup is intended for demonstration purposes only and should be treated as inherently insecure.
</p>

<p style="color:red; font-weight:bold;">
DO NOT USE FOR SENSITIVE APPLICATIONS! This setup is not suitable for any application where data privacy, security, or integrity is a concern. If you need to deploy a service with robust security, you must implement additional security measures, such as HTTPS, user authentication, firewalls, and regular security audits.
</p>

<p style="color:red; font-weight:bold;">
IMMEDIATE ACTION REQUIRED! After testing and validating this prototype, you should immediately take steps to disable or delete the Google Cloud resources you created. Failing to do so may result in unintended security vulnerabilities or unwanted costs.
</p>

<p style="color:red; font-weight:bold;">
USE AT YOUR OWN EXPENSE AND RISK!!!
</p>

---

# Step 1: Write some R code that does something useful

For the purposes of this demo, we will keep this as simple as possible. This R code won't do anything useful. You can swap in some R code that does something useful later. I keep this simple so we can focus on the process of interest.

Here's our R code:

```r
name <- "Bob"
paste0("Hello from our R web service ", name, "!")
```

Which, when run, produces:

```markdown
> Hello from our R web service Bob!
```

# Step 2: Turn your R code into an R function

Here's how we turn this code into an R function:

```r
sayHi <-
  function(name = "Bob")
    {
      list(message = paste0("Hello from our R web service ", name, "!"))
    }
```

Which, when called:

```r
sayHi("Monica")
```

Produces:

```markdown
> Hello from our R web service Monica!
```

# Step 3: Turn your R function into an API using Plumber

Here's how we turn this function into an API using Plumber:

```r
library(plumber)

#* @apiTitle Our Little API
#* @apiDescription Greets the user
#* @param name The user's name
#* @get /greet
function(name = "Bob")
{
  list(message = paste0("Hello from our R web service ", name, "!"))
}
```

You may need to install Plumber and its dependencies first, if this is your first time using it.

# Step 4: Turn your Plumber API into a web service using Google Cloud

## Step 4.1: Log into your Google account

Be sure you are logged into the Google account you want to use. If you don't have a Google account, you can just go to gmail.com and create one. I used a Chrome browser. You might need to be logged into a Chrome browser with your Google account, as well. I'm not sure about that.

## Step 4.2: Create a Google Cloud account

Go to cloud.google.com. If you haven't already set up a Google Cloud account, do so. Click the "Start Free" button to get started doing so. Note that you will get $300 in credit for free. This is plenty for the purposes of this demo. Plug in the basic info about you and the payment info. Then click the "Start Free" button (again). Then plug in something for their silly onboarding questions.

Important! We are now moving into a world where we're using cloud resources that cost money to use. Be sure to carefully monitor costs from here forward. I encourage you to do some reading on how to best do this. And be sure you don't proceed until you are comfortable. It may be worth noting that cost was taken into consideration as I was assembling this approach. The upper limit of what this should cost even if you poorly manage costs for a little while (say on the order of days) should not be much, I don't think. But, again, per the disclaimer at the top of the page, costs are your responsibility, and you shouldn't proceed unless you feel comfortable.

## Step 4.3: Create a Google Cloud project

When you create your account, they create a default project for you called My First Project.  This is fine.  We'll use this.

## Step 4.4: Create a virtual machine using Google Cloud's Compute Engine

Find the Create a VM button below and click it. Then, click Enable to enable the Compute Engine API. We could use other Google Cloud Services to host our web service (e.g., Cloud Run), but, for now, we will keep it simple (and less dangerous) and just use Compute Engine. You may have to wait a bit for Compute Engine to launch. After it has launched, click Create Instance. All the defaults are fine with two very important exceptions. Be sure to check the boxes next to Allow HTTP traffic and Allow HTTPS Traffic under the Firewall section below. Then click Create -- all the way down at the bottom of the window.

## Step 4.5: Install R and Plumber on your Google Cloud Compute Engine virtual machine

We will need to configure our VM. To do this, find the SSH link next to the name of your virtual machine and click it. Then click Authorize (this is where being logged into your Google account comes into play). Now you are in a bash terminal session within your VM. In bash, do the following ...

Be sure your package list is updated by running the following command:

```bash
sudo apt-get update
```

And then wait for it to finish doing its thing.

Then install R by running the following command:

```bash
sudo apt-get install r-base
```

You might have to type Y and hit enter to tell it to go ahead and use the disk space.

And then wait for it to finish doing its thing.

Now, open R in the terminal by running the following command:

```bash
R
```

You will know you are in R because you will see the usual R startup stuff. Also, your cursor will now have > to the left of it.

Now, install Plumber from the R session by running the following command:

```r
install.packages("plumber")
```

It will ask you something about using a personal library. Type yes and hit enter. Then it will ask you about something else. Type yes again and hit enter.

Then wait for it to finish doing its thing. This one takes a few minutes. This is a good time to refill your coffee and pet your dog. And if you are anything like me this is also a good time to feel really grateful for whoever did all the work that makes all the things the machine tells you its doing work.

After all that is done, you will get some warning messages about some non-zero exit statuses.  This is fine.  We will fix this next.

To fix, first, run the following command (again):

```bash
sudo apt-get update
```

Then, run this command:

```bash
sudo apt-get upgrade
```

Again, you might have to type Y and hit enter to tell it to go ahead and use the disk space.

Then wait for it to finish doing its thing. This one also takes a few minutes.

After it has finished, run this command:

```bash
sudo apt-get install build-essential libcurl4-openssl-dev libssl-dev libxml2-dev libgit2-dev
```

Again, you might have to type Y and hit enter to tell it to go ahead and use the disk space.

Then wait for it to finish doing its thing.

Now, run this command to start an R session:

```bash
R
```

Then run this:

```r
install.packages("curl")
```

Then this:

```r
install.packages("sodium")
```

You'll get an error.  Again, this is okay.

Run this command to exit the R session:

```r
quit()
```

Type n and hit enter to decline saving the workspace.

Then, run this command in Bash:

```bash
sudo apt-get install libsodium-dev
```

And then run this:

```bash
export PKG_CONFIG_PATH=/usr/lib/x86_64-linux-gnu/pkgconfig:$PKG_CONFIG_PATH
```

Then, start an R session:

```bash
R
```

And run this from within the R session:

```r
install.packages("sodium")
```

We're all set.

Now, while still in the R session, run:

```r
install.packages("webutils")
```

And then, while still in the R session, run:

```r
install.packages("plumber")
```

You won't see any warnings this time. Plumber is now properly installed.

You can confirm this by running (still in the R session):

```r
library(plumber)
```

Run the following command to exit the R session and return to Bash:

```r
quit()
```

Type n and hit enter to decline saving the workspace image.

## Step 4.6: Host your Plumber API as a web service on your virtual machine

In Step 3 above we turned our function into an API using Plumber:

```r
library(plumber)

#* @apiTitle Our Little API
#* @apiDescription Greets the user
#* @param name The user's name
#* @get /greet
function(name = "Bob")
{
  list(message = paste0("Hello from our R web service ", name, "!"))
}
```

Create an R script on your local machine that contains this code.  Save it as plumber_script.R.

Then, create a separate R script on your local machine that contains this code:

```r
library(plumber)
r <- plumb("plumber_script.R")
r$run(host = "0.0.0.0", port=8000)
```

Save it as start_plumber.R.

Now, go back to your SSH window. Click the Upload File button at the top-right-hand corner of the window. Select the plumber_script.R file you just created and click to upload it.

Now, your SSH window may crash. This is okay. Click to re-authorize. When you do, a new terminal window should open, and you should be in Bash again. (I have no idea why this happens.)

Now, try uploading the file again. It should work. And then upload the start_plumber.R file. It should also work.

Now you can run the following command to see that the files uploaded properly:

```bash
ls
```

This should return:

```markdown
> R  plumber_script.R  start_plumber.R
```

Perfect.

Now we can launch our web service by running the following command:

```bash
Rscript start_plumber.R
```

You will see the following in your Bash session:

```markdown
> Running plumber API at http://0.0.0.0:8000
> Running swagger Docs at http://127.0.0.1:8000/__docs__/
```

Bingo. Your web service is running.

Note that whenever you want to stop your service and return to the Bash command line, you can press CTRL+C (at least that is how it works on my Mac, anyway). For now, do not do this. For now, let the web service run.

## Step 4.7: Test using your web service from within your virtual machine's environment

Don't close the window in which your SSH session is running. We need the web service to continue running in this SSH session.

Return to the screen on which you clicked to start the SSH session in Step 4.5 above. Click the SSH link again -- to start a second SSH session. In this second session, run the following command:

```bash
curl "http://127.0.0.1:8000/greet"
```

You will see the following response from your web service:

```markdown
{"message":["Hello from our R web service Bob!"]}
```

It is working!This is exactly what we programmed our Plumber API to do. The web service is returning its response in JSON format -- the standard format for web services.

You can also pass a different value for your name parameter to the service and watch it react. For instance, you can run:

```bash
curl "http://127.0.0.1:8000/greet?name=Monica"
```

And you will see:

```markdown
{"message":["Hello from our R web service Monica!"]}
```

Beautiful.

It's great that we have it working within in our virtual machine's environment. Now we just need to open up access to it to the outside world.

Leave this window open. We will use it again later.

## Step 4.8: Make your web service publicly accessible

Let's go back to the screen on which you clicked to start the SSH session in the step above again

In the search bar at the top-center of the window, type VPC Network and hit enter. Click the VPC Network link. Find the link to your default network below (it's under VPC Networks and called "default"). Click this link. Then click the Edit button.

Now, under Dynamic routing mode, click to check the circle next to Global. Then click Save.

Then, find the Firewalls tab -- towards the top-middle of the screen. Click on the tab name -- which is a link. Then, click the Add Firewall Rule button. 

Enter "open-port-8000" as the name of the rule. Under the Action on match section, in the Targets box, select "All instances on the network". Then, in the IPv4 ranges box enter "0.0.0.0/0". Then, under the Protocols and ports section, click to check the box to the left of TCP, and in the Ports box enter "8000". Then click Create down below.

Now if you click the drop-down arrow to the left of vpc-firewall-walls you will see your new open-port-8000 rule.

Again, leave this window open.  We will use it again later.

## Step 4.9: Test using your web service from anywhere on the internet

Now, we need to return to the Compute Engine screen within our Google Cloud project. Go back up to the search bar. Enter Compute Engine and click to select it. This will return you to the screen where we clicked SSH above a couple of times. To the left of where we had clicked SSH, find the External IP (it should look something like this: 55.555.555.55). Hover your mouse pointer to the right of the External IP link until the button emerges that enables you to copy it to your clipboard. Click this button to copy it to your clipboard.

Now, open a browser tab, and enter the following in its URL bar:

```markdown
[paste your VM instance's External IP here]:8000/greet?name=Monica
```

Then press enter.

Be sure that when you paste your VM instance's external IP into your browser's URL bar it looks like this (I'm using 5s as placeholders for your IP's actual numbers.): 55.555.555.55:8000/greet.

Your browser will show you the same response from your web service as when we interacted with it from within your VM's environment in Step 4.6 above.  That is:

```markdown
{"message":["Hello from our R web service Bob!"]}
```

Voila! Now anyone who can use the internet can use your web service! It is working!

To show how the service can react dynamically, you can put this into your browser's URL bar, as we did in Step 4.6 above:

```markdown
[paste your VM instance's External IP here]:8000/greet
```

And you will see the same response as we did in Step 4.6 above:

```markdown
{"message":["Hello from our R web service Monica!"]}
```

Beautiful.

# Step 5: Turn your web service into a ChatGPT custom Action using a My GPT

Now we will enable ChatGPT users to interact with our web service natively from ChatGPT!

To do this, we will use ChatGPT's "My GPTs" feature. Note that in order to use the My GPTs feature you must have a ChatGPT Plus or Enterprise account.

## Step 5.1: Wrap your web service in a secure tunnel using Ngrok

ChatGPT requires us to have an HTTPS URL. As of now our URL is an HTTP URL. We will use ngrok as a workaround for this for the purposes of this quick-and-dirty demo.

### Step 5.1.1: Install Ngrok on your VM

Go back to the window that contains the second SSH session we created for Step 4.6 and 4.7 above. Run the following command to download Ngrok:

```bash
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
```

Then, run this command:

```bash
tar -xvzf ngrok-v3-stable-linux-amd64.tgz
```

Then, run this command:

```bash
sudo mv ngrok /usr/local/bin/ngrok
```

And, finally, verify the installation of ngrok by running this command:

```bash
ngrok version
```

You should see something like:

```bash
ngrok version 3.xx.x
```

### Step 5.1.2: Create an Ngrok account

In a new browser tab, go to https://ngrok.com/. Click Sign Up. Click Sign up with Google. Choose the account you used to create your Google Cloud account. Press Continue. Accept the terms. Set up multi-factor authentication as they ask (I just use Google's Authenticator app, which makes it really easy to scan the QR to get this done.) Plug something in for their silly little onboarding questions -- click to select "Development" when you do (I don't know that this matters.). Then, after you have logged into your ngrok account, look on the left-hand side for the link to YourAuthtoken. In the Command Line box, click the link to Show Authtoken. Then click the copy icon to the right of the "ngrok config add-authtoken [your authtoken]" box to copy this command to your clipboard.

### Step 5.1.3: Set your Ngrok authtoken

Now, in your second SSH session, paste the command you just copied and run it.

```bash
ngrok config add-authtoken [your authtoken goes here]
```

You should see something like:

```markdown
Authtoken saved to configuration file: [...]/.config/ngrok/ngrok.yml
```

Where [...] is the correct path prefix given how your VM is configured.

### Step 5.1.4: Run the secure tunnel that contains your running web service

Still in your second SSH session, run this command:

```bash
ngrok http [your VM's External IP]:8000
```

You should see something like:

```markdown
Session Status                online                                                                            
Account                       [your-email]@gmail.com (Plan: Free)                                         
Version                       3.XX.0                                                                            
Region                        United States (us)                                                                
Latency                       XXms                                                                              
Web Interface                 http://127.0.0.1:4040                                                             
Forwarding                    https://XXxX-XX-XXX-XXX-XX.ngrok-free.app -> http://[your VM's External IP]:8000              
                                                                                                                
Connections                   ttl     opn     rt1     rt5     p50     p90                                       
                              0       0       0.00    0.00    0.00    0.00         
```

Note you can use CTRL+C to stop this at any time. But don't do that. Let it run.  In order for ChatGPT to access your web service, you need both the Plumber part (i.e., "Rscript start_plumber.R") and the Ngrok part (i.e., "ngrok http [your VM's External IP]:8000") to be running.

### Step 5.1.5: Test hitting the web service from the secure tunnel

Now, if you copy the first part of the text from your Ngrok session status above (i.e., "https://XXxX-XX-XXX-XXX-XX.ngrok-free.app"), paste it into your browser URL bar, then append "/greet" to the end of it (so, "https://XXxX-XX-XXX-XXX-XX.ngrok-free.app/greet"), and press enter, you should get the usual response:

```markdown
{"message":["Hello from our R web service Bob!"]}
```

And, similarly, if you use:

```markdown
https://0ed6-35-192-176-13.ngrok-free.app/greet?name=Monica
```

You should get:

```markdown
{"message":["Hello from our R web service Monica!"]}
```

Success!

### A special note on Ngrok

Ngrok is a temporary solution for testing purposes. It is not suitable for production. It's extremely insecure. For any serious use, a proper HTTPS setup with certificates is necessary.

## Step 5.2: Configure your ChatGPT custom Action and custom GPT (or "My GPT")

### Step 5.2.1: Create your My GPT

Log into your ChatGPT account. Click on the account icon in the top-right-hand corner of the screen (mine is a circle containing my initials). Then, click the My GPTs link in the drop-down. Then, click the + icon next to Create a GPT at the top of the screen.

This will bring you to the Configure screen for your GPT. Give it a name -- such as "hello-ChatGPT-to-R". In the Description box, enter "Just send me a message that says 'hello' to get started!"

In the Instructions box, copy / paste the following:

```markdown
Greet the user with a message that says, "Welcome to my Custom GPT that calls my R Plumber API via my Google Cloud web service!".

Then, say, "This is how it works. You tell me your name, and then I understand that that's your name, and I send it off to the web service to get a greeting customized for that name. Sound good? Just type your name to get started!".

Then ask, "What is your name?" After the user provides their name, call the web service, using the user's name as the name parameter value for the web service call. Then, respond to the user with, "The web service's response was:" followed by the web service response payload message value exactly as you receive it. Under no circumstances should you deviate from providing the web service response message value payload exactly as you receive it -- verbatim. 

Then, say, "This time just tell me a little bit about yourself, and include your name somewhere along the way. I'll see if I can detect your name and call the API again." After the user provides a response from which you can garner their name, respond to the user with, "I detected your name is" then the user's name, and then, "The web services response was:" followed by the web service response payload message value exactly as you receive it. Under no circumstances should you deviate from providing the web service response payload message value exactly as you receive it -- verbatim.

Then, say, "Thank you for trying me out! For more about how this all works, check out https://my-little-dev-blog.github.io/site/posts/how-to-enable-anyone-to-use-what-you-built-in-R-from-ChatGPT.html. Best wishes to you as you go!"
```

### Step 5.2.2: Create your custom Action

Now, under Actions, click the Create new action button.

First, we will enter our Schema.  I used ChatGPT to create this. I provided it with my two plumber files and copy / pasted the "Forwarding" line from the ngrok Session Status, and then I asked it to create an Open API schema for the API for which I just shared the info with it. It took a few back-and-forths of copy / pasting what it gave me into the Schema box, seeing what error that produced, and then copy / pasting that error back into Chat to get the next iteration. But it ultimately landed on this -- a version that doesn't produce any errors and appears reasonable enough.

```markdown
{
  "schema_version": "1.0",
  "name": "Plumber API Assistant",
  "description": "A bot that helps users interact with a simple R-based Plumber API for greeting users.",
  "info": {
    "title": "Plumber API Assistant",
    "version": "1.0.0",
    "description": "This is a custom ChatGPT experience that interacts with an R-based Plumber API."
  },
  "servers": [
    {
      "url": "https://0ed6-35-192-176-13.ngrok-free.app",
      "description": "Primary server for the Plumber API"
    }
  ],
  "paths": {
    "/greet": {
      "get": {
        "operationId": "getGreet",
        "summary": "Greet the user",
        "description": "Returns a greeting message for the user",
        "parameters": [
          {
            "name": "name",
            "in": "query",
            "description": "The user's name",
            "required": false,
            "schema": {
              "type": "string",
              "default": "Bob"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "A greeting message",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "message": {
                      "type": "string",
                      "example": "Hello from our R web service Bob!"
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  "fallback_intent": {
    "responses": [
      "I'm not sure how to help with that. You can ask me to greet you!"
    ]
  }
}
```

Next, we will provide a link to our privacy policy. Since this is just a little demo, I didn't spend any time on this. I used ChatGPT again.  I just provided ChatGPT with the Open API spec and asked it to create a boilerplate privacy policy for me. Then, I copy / pasted this into a Google doc, and selected File -> Share -> Publish to Web. Then I just grabbed the link and copy / pasted it into the Privacy Policy box.

That's it!

Now just press Create (top-right corner of window) to create your custom GPT. When you do, you can decide if you would like to keep it private to just you, share it with anyone with the link, or even publish it in the GPT Store (you may want to button things up a little more before you do that!).

Then, whenever you want to demo it, just hit that link!

---

<p style="color:red; font-weight:bold;">
REMINDER: IMMEDIATE ACTION REQUIRED! After testing and validating this prototype, you should immediately take steps to disable or delete the Google Cloud resources you created. Failing to do so may result in unintended security vulnerabilities or unwanted costs.
</p>

---

# Closing thoughts

## This is a prototype

This is a prototype. It should be evaluated through that lens. Feel free to send along all the hate mail you like about all the ways it falls short when you move beyond that lens, but unless you are providing criticisms of it through the lens of it being a prototype, I'm going to blissfully ignore it. If you stick to the lens of it being a prototype, and you would be gracious enough to share your reactions within that lens, I will be very grateful and glad to have them! I would love to improve this post! And I very much enjoy working collaboratively to do so.

## This is NOT EVEN CLOSE to secure

Basically no consideration at all has been given to security along the way here. Or performance. Or testing. Or any of the other many considerations that come into play when you move from building a little prototype that works and that you can kill after seeing it works (thereby avoiding almost all of the security risks) to something that you actually use on an ongoing basis. DO NOT USE THIS FOR MORE THAN QUICK BUILD-THEN-KILL PROTOTYPING. Run through the above, see that it works, and then: a) turn off billing on the Google Cloud account you used to build this, b) at least suspend the Google Compute VM on which it is running -- if not just entirely delete the whole of the project.

## Reactions and contributions are welcome!

The best form you can share your feedback is in the form of pull requests that I can just review and then hopefully just accept -- so that your helpful comments take the form of instant improvements to the post. The next best form of feedback is thoughtfully articulated issues that you create here in the repo. Before you create an issue, please try to resolve the issue on your own first, and, if you aren't able to, please at least explain how you tried to, and please be sure to include enough information to reproduce the issue. The last best form of feedback is a thoughtful email to my.little.dev.blog@gmail.com. Please only send an email if you can't even begin to make an attempt at submitting a pull request or an issue. I'll still welcome your feedback even if it comes in this form, but may be less likely to get to it.

---

Click [here](https://github.com/my-little-dev-blog/site/blob/main/posts/how-to-enable-anyone-to-use-what-you-built-in-R-from-ChatGPT.md) to view this post on Github.

---

