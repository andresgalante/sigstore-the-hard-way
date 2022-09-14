# Domain configuration

Now that are instances are running, lets grab the external IP's and set up domains.

> 📝 A cheap temp domain can be grabbed from [Google Cloud Domains](https://console.cloud.google.com/net-services/domains/). Just type in random, nonsensical string
  and you should easily be able to get a domain
  for $1. There are also lots of other providers. Use whatever works for you.

## Configuration

Export a variable that will point to the domain you just bought. In this example we'll be using `example.com`:

```bash
export DOMAIN="example.com"
```

We'll be using this variable throughout the following code-snippets.

### rekor.example.com

Grab your external / public IP:

```bash
Rekor_PIP=$(gcloud compute instances describe sigstore-rekor \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

You now want to make an "A Record" to a subdomain or "rekor" and to your external IP from the above command

To create resource records on Google:

1. Go to [Google Domains](https://domains.google.com/)
2. Click on your domain from the homepage
3. DNS > Manage Custom Records

If you're using GCP as the DNS provider this can be done as follows:

```bash
export ZONE="example-com"

gcloud dns record-sets create rekor.$DOMAIN. \
  --rrdatas=$Rekor_PIP \
  --type=A --ttl=60 --zone=$ZONE
```

|Type|Host| Value|
|---|---|---|
| A Record|rekor|x.x.x.x|

### fulcio.example.com

Now repeat the same for fulcio, and dex:

```bash
Fulcio_PIP=$(gcloud compute instances describe sigstore-fulcio \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

If you're using GCP as the DNS provider this can be done as follows:

```bash
export ZONE="example-com"

gcloud dns record-sets create fulcio.$DOMAIN. \
  --rrdatas=$Fulcio_PIP \
  --type=A --ttl=60 --zone=$ZONE
```

|Type|Host| Value|
|---|---|---|
| A Record|fulcio|x.x.x.x|

### oauth2.example.com

```bash
oauth2_PIP=$(gcloud compute instances describe sigstore-oauth2 \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

If you're using GCP as the DNS provider this can be done as follows:

```bash
gcloud dns record-sets create oauth2.$DOMAIN. \
  --rrdatas=$oauth2_PIP \
  --type=A --ttl=60 --zone=$ZONE
```

|Type|Host| Value|
|---|---|---|
| A Record|oauth2|x.x.x.x|

Check the records-sets generated during this process:

```bash
gcloud dns record-sets list --zone $ZONE
```

> 📝 We do not need a domain for the certificate transparency log. This only
 communicate over a private network to Fulcio.
