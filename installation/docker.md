# Installation - Docker

The officially supported way of installing Refractor is with Docker. Installation on a fresh server without any other web services is recommended to prevent conflicts.

If you haven't already, read through the [installation requirements](/installation/requirements) to ensure you have everything needed to install Refractor.

> As specified on the Requirements page, if you are installing Refractor on a low memory system you should first set up a swapfile to make the build process faster.

### 1. Cloning the source code

The Refractor source code is stored in a Git version control system. To clone the source code, you can use the following Git command.

Clone the Refractor backend source code with the following command:

```bash
git clone git@github.com:RefractorGSCM/Refractor.git
```

And then enter the directory with:

```bash
cd Refractor
```

### 2. Running the quickstart script

Make sure the quickstart script is executable with the following command:

```bash
chmod +x ./quickstart.sh
```

and then execute it with:

```bash
./quickstart.sh
```

### 3. Using the quickstart script

When running the script, you should see an introduction and then your first prompt.

> **Always run the installer in test mode until you're sure that your configuration is correct. Otherwise, you may hit LetsEncrypt rate limits.**

Follow through the prompts while making sure to enter the correct information in each one.

Once the script finishes, check for any errors. Refer to the troubleshooting section below if you see any errors.

Additionally, run `docker ps -a` and check the status of Refractor containers. If any are not online, something went wrong and you should inspect the logs of those containers using `docker logs -f <container ID>`.

Once all your containers are online, try visiting the domain you used in your web browser. You should see a security notice talking about invalid certificates or something similar. This is because when you run the script in test mode, real SSL certificates are not used.

Now, go run through the quickstart script again, **this time in deploy mode**. When asked if you want to overwrite your config changes, enter _c_ to continue with your current files. Continue until the installer finishes, and if all goes well Refractor should now be deployed! Visit your domain to confirm.

### Troubleshooting

If you got errors, check the message to see if you can determine the container which experienced the error.

If it is not immediately obvious, run the following command to see the status of containers:

```bash
docker ps -a
```

If you see that the statuses of some Refractor containers are set to "restarting" or "exited", these containers are not workiong properly and you should inspect their logs with the following command:

```bash
docker logs -f <container ID>
```

Be sure to replace `<container ID>` with the correct container ID.

Some common error scenarios are listed below.

> **"host not found upstream" or similar in nginx logs**

This means that a container did not come online. Often times, this will be the Refractor backend container. You can check the logs of the backend container using the following command: `docker logs -f refractor`.

If you see an error message, try to find it's source. A likely culprit could be database connection.

> **LetsEncrypt "duplicate certificate" or "too many certificates" or similar**

This error means that you have hit the LetsEncrypt rate limit. You can find more info about their rate limits (here)[https://letsencrypt.org/docs/rate-limits/].

Unfortunately, there is very little you can do in this case. This is why it's highly recommended that you run the script in _test_ mode until you're sure that everything is functioning correctly.
