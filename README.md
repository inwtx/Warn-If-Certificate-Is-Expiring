# Warn-If-Certificate-Is-Expiring
  
This script will warn you if your certificate is or has expired.  It will give a 5 day warning and continue daily until the script is stopped or your certificate is renewed.  

```
#!/bin/bash
#
# Ystats v2.0
#
#
#---------------------------------------------------------------------#
#                --- Server Certificate Valuator ---                  #
#                                                                     #
# The following script will notify you by email daily when your       #
# certificate is about to or has expired.  Create a file CertCheck.sh #
# and place this code therein.  Then make it executable:              #
# sudo chmod -R 755 /path/to/CertCheck.sh                             #
# Your certificate path must be in 'certpath=' below.                 #
# Place your private email address in the 'myemailaddr=' field below. #
#                                                                     #
# Retrieve stastics for server web page - cron job                    #
# 0 6 * * * /path/to/CertCheck.sh        # run at 0600 daily          #
#                                                                     #
#---------------------------------------------------------------------#

export PATH=$PATH:/usr/sbin
export PATH=$PATH:/sbin
export PATH=$PATH:/bin

certpath="/etc/ssl/private/letsencrypt-domain.pem"
myemailaddr="<your private email address>"     # your private email address without brackets <>


SSLvar=$(openssl x509 -enddate -noout -in $certpath)
SSLvar=$(cut -d'=' -f2 <<<$SSLvar)
SSLvar=$(awk '{print $1"-"$2"-"$4" "$3" "$5}' <<<$SSLvar)
SSLvar1=$(awk '{print $1}' <<<$SSLvar)
SSLvar2=$(let DIFF=(`date +%s -d $SSLvar1` - `date +%s`)/86400 && echo $DIFF)

if [[ $SSLvar2 -le 5 ]]; then   # if 5 or less days left, send email warning
   ## choose mutt or mail below to email warning
#   echo "Certificate expiring in 5 days!" | mutt -s "Notice: Certificate expiring!" $myemailaddr
   echo "Certificate expiring in 5 days!" | mail -s "Notice: Certificate expiring!" $myemailaddr -r me@my.server
fi

exit 0
```
