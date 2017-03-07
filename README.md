# Setup
Install Node.js<br>
We will install the latest LTS release of Node.js, on the app server.<br>
On the app server, let's update the apt-get package lists with this command:<br>

sudo apt-get update<br>
Then use apt-get to install the git package, which npm depends on:<br>
sudo apt-get install git<br>
Go to the Node.js Downloads page and find the Linux Binaries (.tar.xz) download link.
Right-click it, and copy its link address to your clipboard. At the time of this writing, the latest LTS release is 4.2.3. If you prefer to install the latest stable release of Node.js, go to the appropriate page and copy that link.<br><br>

Change to your home directory and download the Node.js source with wget. Paste the download link in place of the highlighted part:<br>

cd ~ <br>
wget https://nodejs.org/dist/v4.2.3/node-v4.2.3-linux-x64.tar.gz<br>
Now extract the tar archive you just downloaded into the node directory with these commands:<br>

mkdir node<br>
tar xvf node-v*.tar.?z --strip-components=1 -C ./node<br>
If you want to delete the Node.js archive that you downloaded, since we no longer need it, change to your home directory and use this rm command:<br>

cd ~<br>
rm -rf node-v*<br>
Next, we'll configure the global prefix of npm, where npm will create symbolic links to installed Node packages, to somewhere that it's in your default path. We'll set it to /usr/local with this command:<br>

mkdir node/etc<br>
echo 'prefix=/usr/local' > node/etc/npmrc<br>
Now we're ready to move the node and npm binaries to our installation location. We'll move it into /opt/node with this command:<br>

sudo mv node /opt/<br>
At this point, you may want to make root the owner of the files:<br>

sudo chown -R root: /opt/node<br>
Lastly, let's create symbolic links of the node and npm binaries in your default path. We'll put the links in /usr/local/bin with these commands:<br>

sudo ln -s /opt/node/bin/node /usr/local/bin/node<br>
sudo ln -s /opt/node/bin/npm /usr/local/bin/npm<br>
Verify that Node is installed by checking its version with this command:<br>

node -v<br>
The Node.js runtime is now installed, and ready to run an application! Let's write a Node.js application.<br>

#Create Node.js Application<br>
Now we will create a Hello World application that simply returns "Hello World" to any HTTP requests. This is a sample application that will help you get your Node.js set up, which you can replace it with your own application--just make sure that you modify your application to listen on the appropriate IP addresses and ports.<br><br>

Because we want our Node.js application to serve requests that come from our reverse proxy server, web, we will utilize our app server's private network interface for inter-server communication. Look up your app server's private network address.<br><br>

If you are using a DigitalOcean droplet as your server, you may look up the server's private IP address through the Metadata service. On the app server, use the curl command to retrieve the IP address now:<br><br>

curl -w "\n" http://169.254.169.254/metadata/v1/interfaces/private/0/ipv4/address <br>
You will want to copy the output (the private IP address), as it will be used to configure our Node.js application.<br><br>

Hello World Code<br><br>

Next, create and open your Node.js application for editing. For this tutorial, we will use vi to edit a sample application called hello.js:<br><br>

cd ~<br>
vi hello.js<br>
Insert the following code into the file, and be sure to substitute the app server's private IP address for both of highlighted APP_PRIVATE_IP_ADDRESS items. If you want to, you may also replace the highlighted port, 8080, in both locations (be sure to use a non-admin port, i.e. 1024 or greater):<br><br>

hello.js<br>
var http = require('http');<br>
http.createServer(function (req, res) {<br>
  res.writeHead(200, {'Content-Type': 'text/plain'});<br>
  res.end('Hello World\n');<br>
}).listen(8080, 'APP_PRIVATE_IP_ADDRESS');<br>
console.log('Server running at http://APP_PRIVATE_IP_ADDRESS:8080/');<br>
Now save and exit.<br><br>

This Node.js application simply listens on the specified IP address and port, and returns "Hello World" with a 200 HTTP success code. This means that the application is only reachable from servers on the same private network, such as our web server.<br><br>

Test Application (Optional)<br>
If you want to test if your application works, run this node command on the app server:<br>

node hello.js<br><br>
Note: Running a Node.js applicat<br>ion in this manner will block additional commands until the application is killed by pressing CTRL+C.<br><br>

In order to test the application, open another terminal session and connect to your web server. Because the web server is on the same private network, it should be able to reach the private IP address of the app server using curl. Be sure to substitute in the app server's private IP address for APP_PRIVATE_IP_ADDRESS, and the port if you changed it:<br><br>

curl http://APP_PRIVATE_IP_ADDRESS:8080<br>
If you see the following output, the application is working properly and listening on the proper IP address and port:

Output:<br>
Hello World
If you do not see the proper output, make sure that your Node.js application is running, and configured to listen on the proper IP address and port.<br><br>

On the app server, be sure to kill the application (if you haven't already) by pressing CTRL+C.<br>
