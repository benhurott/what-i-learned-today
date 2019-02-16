# Android: OAuthActivity

Use this activity to login with any oauth service that use the correct specs.

## Usage

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == OauthActivity.OAUTH_LOGIN_REQUEST) {
        if (resultCode == OauthActivity.OAUTH_LOGIN_SUCCESS) {
            String accessCode = data.getStringExtra("accessToken");
            Log.d("token", accessCode);
        }
        else if (resultCode == OauthActivity.OAUTH_LOGIN_ERROR) {
            String errorMessage = data.getStringExtra("error");
            Log.d("error", errorMessage);
        }
    }
}

private void openOauthLogin() {
    Intent intent = new Intent(this, OauthActivity.class);
    String clientId = "my-client-id";
    String clientSecret = "my-client-secret";

    intent.putExtra("title", "Signin with Github");
    intent.putExtra("login_url", "https://github.com/login/oauth/authorize?client_id="+clientId+"&scope=user%20public_repo");
    intent.putExtra("client_id", clientId);
    intent.putExtra("client_secret", clientSecret);
    intent.putExtra("access_token_url", "https://github.com/login/oauth/access_token");

    startActivityForResult(intent, OauthActivity.OAUTH_LOGIN_REQUEST);
}
```

The intent args are:

| name | type | required | description |
| ---- | ---- | -------- | ----------- |
| title | string | yes | the title of the activity |
| login_url | string | yes | the login url of the oauth that will be opened when the activity starts |
| client_id | string | yes | the client_id of your oauth application |
| client_secret | string | yes | the client_secret of your oauth application |
| access_token_url | string | yes | the url to get the final access token |


## Source

```java
package br.com.nextfleet.nextfleet;

import android.app.Activity;
import android.content.Intent;
import android.graphics.Bitmap;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.webkit.WebView;
import android.webkit.WebViewClient;

import com.android.volley.AuthFailureError;
import com.android.volley.Request;
import com.android.volley.Response;
import com.android.volley.VolleyError;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.StringRequest;
import com.android.volley.toolbox.Volley;

import org.json.JSONObject;

import java.io.DataOutputStream;
import java.net.URL;
import java.util.HashMap;
import java.util.Map;

import javax.net.ssl.HttpsURLConnection;

public class OauthActivity extends AppCompatActivity {

    static final int OAUTH_LOGIN_REQUEST = 123540;
    static final int OAUTH_LOGIN_SUCCESS = 123541;
    static final int OAUTH_LOGIN_ERROR = 123542;

    private WebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_oauth);
        setTitle(getCustomTitle());

        configureWebView();

        webView.loadUrl(this.getLoginUrl());
    }

    private String getCustomTitle() {
        return this.getIntent().getStringExtra("title");
    }

    private String getLoginUrl() {
        return this.getIntent().getStringExtra("login_url");
    }

    private String getClientId() {
        return this.getIntent().getStringExtra("client_id");
    }

    private String getClientSecret() {
        return this.getIntent().getStringExtra("client_secret");
    }

    private String getAccessTokenUrl() {
        return this.getIntent().getStringExtra("access_token_url");
    }

    private void configureWebView() {
        webView = findViewById(R.id.webview);

        webView.getSettings().setJavaScriptEnabled(true);

        webView.setWebViewClient(new WebViewClient() {
            @Override public void onPageStarted(WebView view, String url, Bitmap favicon){
                super.onPageStarted(view, url, favicon);
            }

            @Override
            public void onPageFinished(WebView view, String url) {
                super.onPageFinished(view, url);

                handleUrlResponse(url);
            }
        });
    }

    private void handleUrlResponse(String url) {
        String authCode = "";

        if (url.contains("?code=")) {
            Uri uri = Uri.parse(url);
            authCode = uri.getQueryParameter("code");
            getAccessTokenForCode(authCode);
        }else if(url.contains("error=access_denied")){
            resolveWithError("Access denied.");
        }
    }

    private void getAccessTokenForCode(final String code) {
        try {
            String url = getAccessTokenUrl();

            StringRequest jsonRequest = new StringRequest(Request.Method.POST, url, new Response.Listener<String>() {
                @Override
                public void onResponse(String response) {
                    Log.d("response", response);
                    resolveWithAccessTokenResult(response);
                }
            }, new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    resolveWithError(error.getMessage());
                }
            }) {
                @Override
                protected Map<String, String> getParams() throws AuthFailureError {
                    Map params = new HashMap();
                    params.put("client_id", getClientId());
                    params.put("client_secret", getClientSecret());
                    params.put("code", code);
                    return params;
                }
            };

            Volley.newRequestQueue(this).add(jsonRequest);

        } catch (Exception e) {
            Log.d("Error", e.getMessage());
            e.printStackTrace();
        }
    }

    private void resolveWithAccessTokenResult(String oauthResponse) {
        String accessToken = getAccessTokenFromOauthResponse(oauthResponse);
        Intent resultIntent = new Intent();
        resultIntent.putExtra("accessToken", accessToken);
        setResult(OAUTH_LOGIN_SUCCESS, resultIntent);
        finish();
    }

    private void resolveWithError(String errorMessage) {
        Intent resultIntent = new Intent();
        resultIntent.putExtra("error", errorMessage);
        setResult(OAUTH_LOGIN_ERROR, resultIntent);
        finish();
    }

    private String getAccessTokenFromOauthResponse(String oauthResponse) {
        String accessCode = oauthResponse.split("&")[0].split("=")[1];
        return accessCode;
    }
}
```