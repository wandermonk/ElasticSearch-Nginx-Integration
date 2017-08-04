# ElasticSearch-Nginx-Integration
The ElasticSearch and Kibana dashboards does not provide Authentication and Authorization out of the box. Hence, Nginx integration with elasticsearch would help in doing the Authorization and Authentication.

Step1: install the openssl tool downloaded from the following link http://gnuwin32.sourceforge.net/packages/openssl.htm ,install nginx and elasticsearch.

Step2: encrypt the password for a specific user and keep it in a file Ex: passwords.txt using the command as below

openssl passwd -crypt "enter the password you want to encrypt here"  (Attached the sample file for your reference)

Step3: Add the config in the nginx.conf file

Server {
            #port at which our nginx server is listening to.
            listen 8070;
            
            auth_basic "Protected Elasticsearch";
            #location of the file which contains our usernames and password.
            auth_basic_user_file passwords.txt;
            
            location / {
               #the proxy_pass is nothing but our kibana or elasticsearch url.
               proxy_pass http://localhost:9200;
               proxy_redirect off;
            }
}

Post configuration 

C:\>curl -i admin:3mvBCibKNfmus@localhost:8070

HTTP/1.1 200 OK
Server: nginx
Date: Fri, 04 Aug 2017 08:35:45 GMT
Content-Type: application/json; charset=UTF-8
Content-Length: 330
Connection: keep-alive

{
  "name" : "mypc",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "bfibqFyoRSKPNx916FkNoA",
  "version" : {
    "number" : "5.5.0",
    "build_hash" : "260387d",
    "build_date" : "2017-06-30T23:16:05.735Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.0"
  },
  "tagline" : "You Know, for Search"
}


C:\>curl -i baduser:3mvBCibKNfmus@localhost:8070

HTTP/1.1 401 Unauthorized
Server: nginx
Date: Fri, 04 Aug 2017 09:18:34 GMT
Content-Type: text/html
Content-Length: 188
Connection: keep-alive
WWW-Authenticate: Basic realm="Protected Elasticsearch"

<html>
<head><title>401 Authorization Required</title></head>
<body bgcolor="white">
<center><h1>401 Authorization Required</h1></center>
<hr><center>nginx</center>
</body>
</html>
