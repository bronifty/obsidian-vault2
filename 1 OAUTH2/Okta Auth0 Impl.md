https://oauth.school/exercise/introduction/

1) create api resource to protect with access tokens
	- name it and provide an identifier
		- the identifier will be the value of the audience claim in the access tokens Auth0 creates
		- use a url that represents your api (the audience will be consuming the access token, which means the api aka resource server (or a reverse proxy/ api gateway filtering the request) will be consuming the access token and is therefore the 'audience')
2) set a default audience for the tenant (account in auth0 such as dev)
by setting a default audience, the access tokens issued by Auth0 will have this audience value by default.


New Auth0 Tenant Onboarding
##### Download and follow these steps:

To run the sample follow these steps:

1. Set the **Allowed Callback URLs** in the [Application Settings](https://manage.auth0.com/#/applications/PkTsoEzF44NNTXTvq6SIf5wuppiuqBba/settings) to:

```
http://localhost:3000/callback
```

2. Set the **Allowed Logout URLs** in the [Application Settings](https://manage.auth0.com/#/applications/PkTsoEzF44NNTXTvq6SIf5wuppiuqBba/settings) to:

```
http://localhost:3000
```

3. Make sure [Node.JS LTS](https://nodejs.org/en/download/) is installed and execute the following commands in the sample's directory:

```
npm install
npm start
```

You can also run it from a [Docker](https://www.docker.com/) image with the following commands:

```
# In Linux / macOS
sh exec.sh
# In Windows' Powershell
./exec.ps1
```