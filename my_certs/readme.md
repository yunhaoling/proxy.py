1. use openssl to generate certificate and private for proxy server and client

openssl req -nodes -x509 -newkey rsa:4096 -keyout client-key.pem -out client-cert.pem -days 365
openssl req -nodes -x509 -newkey rsa:4096 -keyout server-key.pem -out server-cert.pem -days 365

note:
I'm setting up a local proxy for test, so make sure when generating certs and keys, the fqdn matches the ip of the proxy/client, other certificates won't work

2. update code in utils.py.wrap_socket to enable client side authentication

ctx.verify_mode = ssl.CERT_REQUIRED
client_ca_cert_path = r"D:\Projects\proxy.py\my_certs\client-cert.pem"  # client cert path
logging.warning('I am loading utlis.wrap_socket')
ctx.load_verify_locations(cafile=client_ca_cert_path)

3. start the proxy server with the following command

proxy --hostname 192.168.1.4 --cert-file .\proxy.py\my_certs\server-cert.pem --key-file .\proxy.py\my_certs\server-key.pem --ca-cert-file .\proxy.py\my_certs\client-cert.pem

4. run the curl command to verify

curl -x https://192.168.1.4:8899 --proxy-cacert ./proxy.py/my_certs/server-cert.pem --proxy-cert ./proxy.py/my_certs/client-cert.pem --proxy-key ./proxy.py/my_certs/client-key.pem https://httpbin.org/get