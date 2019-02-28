# Getting client and subscription ID:

➜  packer az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
Retrying role assignment creation: 1/36
Retrying role assignment creation: 2/36
Retrying role assignment creation: 3/36
{
  "client_id": "41cb502c-4292-4cc8-a4df-f04c72660c1e",
  "client_secret": "11e70949-4a53-45dd-94e7-7fced8539882",
  "tenant_id": "a286e73c-9709-4e12-b4af-036437a828a9"
}
➜  packer az account show --query "{ subscription_id: id }"
{
  "subscription_id": "ba0583bb-4130-4d7b-bfe4-0c7597857323"
}


Body of config download:
{
  "vpnSites": [ "/subscriptions/ba0583bb-4130-4d7b-bfe4-0c7597857323/resourceGroups/WANTestRG/providers/Microsoft.Network/vpnSites/Fred-demo-site1"
  ]
}




Express route - 40Gbps, 100Gbps
Expansion of Virtual WAN
Virtual network TAP

	"subscriptionID": "ba0583bb-4130-4d7b-bfe4-0c7597857323",

Client secret:
satdehr9F26Y8Wufj1E2yjXs5wW/N6xDqWkZj1t1kic=

Tenant ID:
a286e73c-9709-4e12-b4af-036437a828a9

Client ID:
	"clientID": "41a3f6bd-b522-4495-9afe-9f769b1ac816",
    "clientSecret":"lVMtZDLsRgqwuXu8GhX8cfRKfrrWHpMGRh254PH/Uyw="

This needs Tenant ID and Client ID
https://login.microsoftonline.com/a286e73c-9709-4e12-b4af-036437a828a9/oauth2/authorize?client_id=41a3f6bd-b522-4495-9afe-9f769b1ac816&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2F&response_mode=query

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```




Then you get token, alternatively you get token at the start.

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJodHRwczovL2FyaXN0YW5ldHdvcmtzLmNvbS85ZDM2NDRjZS1jMjU3LTQzYzctYjI1Ny1hNjNlMzViMjExNTAiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9hMjg2ZTczYy05NzA5LTRlMTItYjRhZi0wMzY0MzdhODI4YTkvIiwiaWF0IjoxNTM3NDczODYwLCJuYmYiOjE1Mzc0NzM4NjAsImV4cCI6MTUzNzQ3Nzc2MCwiYWlvIjoiNDJCZ1lOaVRiekJwOTRHRVF6N1haVCtFaWt4ZUFBQT0iLCJhcHBpZCI6IjQxYTNmNmJkLWI1MjItNDQ5NS05YWZlLTlmNzY5YjFhYzgxNiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0L2EyODZlNzNjLTk3MDktNGUxMi1iNGFmLTAzNjQzN2E4MjhhOS8iLCJvaWQiOiI4ODkwZTA4YS1jMDRhLTQ0MjYtOTQzYi00NzhmOTliNGE1NDIiLCJzdWIiOiI4ODkwZTA4YS1jMDRhLTQ0MjYtOTQzYi00NzhmOTliNGE1NDIiLCJ0aWQiOiJhMjg2ZTczYy05NzA5LTRlMTItYjRhZi0wMzY0MzdhODI4YTkiLCJ1dGkiOiJpcmpqM2NfejZrV2xrM3BESmVVRkFBIiwidmVyIjoiMS4wIn0.HzGTs7F3YV7jMxuZZVK0to87Zfh09lBGrxhKxPtuiiBHYovnjjGiIwUYKhj3y6olBejqolTR9VO_I_eA-unUfCLiGiA3xDtG5hpW0Ozyfbvz64V_TwuhgGqY-b54pfNBMCgjlI_V5AE6TnqQoMZgzukhWXfeA_5UHalziGn7mD4-24pyoVi0v9s0duGXv385L-czUoY5iTL1CIcESJ3Nd2OsnU_Ks3N3UDwEr813VbrMTJC-fJiDrSsrjCDGm0ZEXVThvxlDVql4CCngVSZW5UFF1xKlbzN7enDdL5y_e0FCM9SgopbIYqXsONTT9d2URcG0kNtZbSKJyXbliq6P0A

VPN Gateway:
e0404622ded9-440a-8e27-81bd26d487c5-westus2-gw


software injected into the VM when booted up to go around UDR's within azure that would come up as part of the devops process
Could the virtual router work within the VM

Look at Gigamon VTAP
Extension - curaed MSI
How will this work with Windows

Palo Alto virtual appliance as the standard for NVA

How can we rip out / shim the network stack of VM


BD Sell through for public cloud
Dabbling with different NFV with JunOS
Focused on overall engagement w/ Azure/AWS/GCP
GTM motions
Likes the public cloud space - lots of potential
Understand SW subscriptions instead of HW only
Former SE/AM - understands different use cases infrastructure efforts are differnent
SW subscriptions is a volume biz
Technical background works with engineering teams

Device UI to add service principal auth data
Upload branch config to Azure (VPNSite) -- CloudVision or configure using VWAN UI 
Azure portal to create hub and vpn endpoint within the hub
Download device config file
Apply config to device
