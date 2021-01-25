# 

When creating a new dotnet application using Azure AD Authentication the implicit auth flow is used by default, according to the documentation you should prefer the auth code flow instead [Prefer the auth code flow](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-implicit-grant-flow#prefer-the-auth-code-flow) 
![By default Implicit Grant is preferred](https://raw.githubusercontent.com/juligu/authcodenetcore/master/doc/images/implicitG1.jpg)

In order to make your application work with auth code flow instead you should make some small changes to the startup class, ConfigureServices method:
```c#
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
})
    .AddCookie()
    .AddOpenIdConnect(options => {
        options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = Configuration["AzureAd:Instance"] + Configuration["AzureAd:TenantId"];
        options.RequireHttpsMetadata = true;
        options.ResponseType = OpenIdConnectResponseType.Code;
        options.ClientId = Configuration["AzureAd:ClientId"];
        options.ClientSecret = Configuration["AzureAd:ClientSecret"];
        options.GetClaimsFromUserInfoEndpoint = true;
        options.Scope.Add("openid");
        options.Scope.Add("profile");
        options.SaveTokens = true;
        options.TokenValidationParameters = new TokenValidationParameters
        {
            NameClaimType = "name",
            RoleClaimType = "groups",
            ValidateIssuer = true
        };
    });
```

Then, you'll be able to disable implicit grant on your application registration auth configuration
![Disable implicit grand](https://raw.githubusercontent.com/juligu/authcodenetcore/master/doc/images/implicitG2.jpg)

