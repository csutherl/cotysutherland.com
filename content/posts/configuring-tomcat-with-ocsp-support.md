+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2019-05-31T19:46:17Z
description = ""
draft = true
slug = "configuring-tomcat-with-ocsp-support"
title = "Configuring Tomcat with OCSP Support"
tags = ["Apache Tomcat"]

+++


Here are some notes from my testing of Apache Tomcat with OCSP support enabled with the APR Connector configured.

The openssl OCSP responder that's running didn't match the certificate's URI. You were running it on port 8088 but the cert has no defined port, which defaults to 80. I updated that to the following command:

$ openssl.exe ocsp -index c:\tmp\certs\ca.db -port 127.0.0.1:80 -rsigner c:\tmp\certs\ca.pem -CA c:\tmp\certs\ca.pem -out c:\tmp\certs\ocsp_responder.out -text

And to verify the certificate's responder URI:

$ openssl x509 -noout -ocsp_uri -in c:\tmp\certs\america.test.example.com.crthttp://127.0.0.1

Now that I got that sorted, I tried to manually verify that it works correctly with openssl (excluding tomcat-native). Here is the response I got back showing that the certificate is revoked and also logging a complaint about the CA being self signed (which I guess isn't an issue since the verification worked as excepted):

$ openssl ocsp -issuer c:\tmp\certs\ca.pem -cert c:\tmp\certs\america.test.example.com.crt -text -url http://127.0.0.1....Response Verify Failure4294956672:error:27069065:OCSP routines:OCSP_basic_verify:certificate verify error:ocsp_vfy.c:138:Verify error:self signed certificatec:\tmp\certs\america.test.example.com.crt: revokedThis Update: Jan 26 17:15:12 2018 GMTReason: supersededRevocation Time: Jan 19 15:52:53 2018 GMT

*** This verify failure does not occur when I test on my Fedora machine***

I noted that even though a request was made to the OCSP responder in the test, there was nothing logged in stdout/stderror or the output log. After some investigation it looks like using -out doesn't work, so I removed it and now you see the request/response data logged in stdout of the terminal. I also added `-nrequest 1` to the responder so that it exits after processing 1 request for more visibility on when it's working.

After I got the openssl only test working I tried a request with openssl s_client:

# openssl s_client -connect localhost:8443 -cert c:\tmp\certs\america.test.example.com.crt -key c:\tmp\certs\america.test.example.com.key -state -CAfile c:\tmp\certs\ca.pem

This is NOT making a request to the OCSP responder and returns a successful connection :(

This leads me to believe that the OCSP support in tomcat is somehow not working. I think I need to debug tomcat-native to verify :(

