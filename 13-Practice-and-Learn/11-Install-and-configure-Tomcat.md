# Apache Tomcat Installation and WAR Deployment

## Scenario

A Java web application needs to be deployed on a Linux application server using Apache Tomcat.

The requirements are:

- Install and enable Tomcat.
- Configure Tomcat to listen on port `3004`.
- Transfer a `ROOT.war` application file from the jump host to the application server.
- Deploy the WAR file so the application is served directly from the Tomcat root URL.

---

## 1. Install and Enable Tomcat

On the application server:

```bash
sudo dnf install -y tomcat
```

Enable and start the service:

```bash
sudo systemctl enable --now tomcat
```

Verify:

```bash
systemctl status tomcat
```

Tomcat requires Java, so it is also useful to confirm that Java is available:

```bash
java -version
```

---

## 2. Change the Tomcat HTTP Port

Tomcat normally listens on port `8080`.

Its main configuration file is commonly:

```text
/etc/tomcat/server.xml
```

Edit the file:

```bash
sudo vi /etc/tomcat/server.xml
```

Find the HTTP Connector:

```xml
<Connector port="8080"
           protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

Change the port to:

```xml
<Connector port="3004"
           protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

Restart Tomcat:

```bash
sudo systemctl restart tomcat
```

Verify that the new port is listening:

```bash
ss -lntp | grep 3004
```

---

## 3. Transfer the WAR File

From the jump host, copy the application to the app server:

```bash
scp ROOT.war tony@stapp01:
```

The trailing colon is important. It tells `scp` that the destination is a remote host.

This copies the file to Tony's home directory:

```text
/home/tony/ROOT.war
```

An explicit version would be:

```bash
scp ROOT.war tony@stapp01:/home/tony/
```

---

## 4. Deploy the WAR File

Tomcat deploys applications placed inside its `webapps` directory.

On the application server:

```bash
sudo mv /home/tony/ROOT.war /var/lib/tomcat/webapps/
```

Depending on the installation method, the Tomcat deployment directory may differ, so verify it before moving the file.

Restart Tomcat if necessary:

```bash
sudo systemctl restart tomcat
```

Tomcat normally detects WAR files automatically and expands/deploys them.

---

## Why `ROOT.war` Is Special

A normal WAR file such as:

```text
myapp.war
```

is usually served at:

```text
http://server:3004/myapp/
```

However:

```text
ROOT.war
```

is deployed as Tomcat's root application.

Therefore it becomes available at:

```text
http://server:3004/
```

---

## Troubleshooting

Check the Tomcat service:

```bash
systemctl status tomcat
```

Check the listening port:

```bash
ss -lntp | grep 3004
```

Verify the WAR file:

```bash
ls -l /var/lib/tomcat/webapps/
```

Inspect recent logs:

```bash
journalctl -u tomcat -n 50
```

---

## Key Takeaway

The deployment flow is:

```text
Jump Host
   |
   | scp ROOT.war
   v
Application Server
   |
   | move into webapps/
   v
Tomcat
   |
   | port 3004
   v
Java Web Application
```

Tomcat provides the runtime for Java web applications, while WAR files provide a convenient packaged deployment format.