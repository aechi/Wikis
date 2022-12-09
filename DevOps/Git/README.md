# Disables ssl verfication in the local repo
git config http.sslVerify false
# or, in ".git/config" add the following (the name of the server is optional)
[http "https://gitlab.dev"]
        sslVerify = false
# or, also prepend to each command
SET GIT_SSL_NO_VERIFY=true && git some operation
