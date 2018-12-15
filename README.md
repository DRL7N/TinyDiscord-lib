# TinyPHP-Discord
Simple Tiny PHP Library to use in your Discord oAuth2

## Example Code

```php


    session_start();

    // The Auth System
    $tinyDiscord = new tinyDSAuth(array(
        'id' => '',
        'scope' => array('identify'),
        'permissions' => 0,
        'redirect' => '',
        'secret' => '',
        'state' => '',
    ));

    // is Token? Use the token here to test

    // Inside the tinyDSAuth::getUser you can insert the value refresh to auto refresh the token if the token is expired
    
    if (isset($_GET['refresh'])) {

        $token = $tinyDiscord->refreshToken($_GET['refresh']);

        if ((isset($token['err']) == false) && (isset($token['data']->error) == false)) {

            $tiny_user = tinyDSAuth::getUser(array(
                'token' => $token['data']->access_token, 'type' => 'users/@me'
            ));

            if ((isset($tiny_user['err']) == false) && (isset($tiny_user['data']->error) == false)) {

                // Show some token details
                echo '<h2>Token details:</h2>';
                echo 'Token: ' . $token['data']->access_token . "<br/>";
                echo 'Refresh token: ' . $token['data']->refresh_token . "<br/>";
                echo 'Token Type: ' . $token['data']->token_type . "<br/>";

                echo 'Expires: ' . date("m/d/Y h:i:s A T", $tinyDiscord->getExpiration()) . " - ";
                echo ($tinyDiscord->hasExpired() ? 'expired' : 'not expired') . "<br/>";

                echo '<h2>Resource owner details:</h2>';
                printf('Hello %s#%s!<br/><br/>', $tiny_user['data']['username'], $tiny_user['data']['discriminator']);

            }

        }

        echo '<pre>';
        print_r($tiny_user);
        echo '</pre>';

        echo '<pre>';
        print_r($token);
        echo '</pre>';

    } else

    if (isset($_GET['token'])) {

        // Get user info
        $tiny_user = tinyDSAuth::getUser(array(
            'token' => $_GET['token'], 'type' => 'users/@me'
        ));

        if ((isset($tiny_user['err']) == false) && (isset($tiny_user['data']->error) == false)) {

            // Show some token details
            echo '<h2>Token details:</h2>';
            echo 'Token: ' . $_GET['token'] . "<br/>";
            echo 'Refresh token: ' . $_GET['refresh_token'] . "<br/>";

            echo '<h2>Resource owner details:</h2>';
            printf('Hello %s#%s!<br/><br/>', $tiny_user['data']['username'], $tiny_user['data']['discriminator']);

        }

        echo '<pre>';
        print_r($tiny_user);
        echo '</pre>';

    } else

    // is code from the oAuth2? Use the code here
    if (isset($_GET['code'])) {

        // Protection
        if (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

            unset($_SESSION['oauth2state']);
            http_response_code(405);

            echo 'ERROR STATE';

        } else {

            // Get the user token
            $token = $tinyDiscord->getToken($_GET['code']);

            if ((isset($token['err']) == false) && (isset($token['data']->error) == false)) {

                // Get the user info
                $tiny_user = tinyDSAuth::getUser(array(
                    'token' => $token['data']->access_token, 'type' => 'users/@me'
                ));

                if ((isset($tiny_user['err']) == false) && (isset($tiny_user['data']->error) == false)) {

                    // Show some token details
                    echo '<h2>Token details:</h2>';
                    echo 'Token: ' . $token['data']->access_token . "<br/>";
                    echo 'Refresh token: ' . $token['data']->refresh_token . "<br/>";
                    echo 'Token Type: ' . $token['data']->token_type . "<br/>";

                    echo 'Expires: ' . date("m/d/Y h:i:s A T", $tinyDiscord->getExpiration()) . " - ";
                    echo ($tinyDiscord->hasExpired() ? 'expired' : 'not expired') . "<br/>";

                    echo '<h2>Resource owner details:</h2>';
                    printf('Hello %s#%s!<br/><br/>', $tiny_user['data']['username'], $tiny_user['data']['discriminator']);



                }

            }

            echo '<pre>';
            print_r($tiny_user);
            echo '</pre>';

            echo '<pre>';
            print_r($token);
            echo '</pre>';

        }

    } else {

        // Send the user into the Discord oAuth2
        $_SESSION['oauth2state'] = $tinyDiscord->getState();
        header('Location: ' . $tinyDiscord->getURL());

    }


```