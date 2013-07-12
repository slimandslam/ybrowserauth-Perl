NAME
    Yahoo::BBAuth - Perl interface to the Yahoo! Browser-Based
    Authentication.

SYNOPSIS
          my $bbauth = Yahoo::BBAuth->new(
              appid  => $appid,
              secret => $secret,
          );
          # Get your appid and secret by registering your application here:
          # https://developer.yahoo.com/wsregapp/index.php
          # Create an authentication link
          printf '<a href="%s">Click here to authorize</a>', $bbauth->auth_url; 
          # You can include some application data or return a user hash using optional params:
          printf '<a href="%s">Click here to authorize</a>', $bbauth->auth_url(
              send_userhash  => '1',
              appdata => 'someappdata',
              );  
          # After the user authenticates successfully, Yahoo returns the user to the page you
          # dictated when you signed up. To verify whether authentication succeeded, you need to
          # validate the signature:
          if (!$bbauth->validate_sig()) {
          print '<h2>Authentication Failed. Error is: </h2>'.$bbauth->{sig_validation_error};
          exit(0);
          }
          # You can then make an authenticated web service call on behalf of the user
          # For Yahoo! Mail:
          my $json = $bbauth->make_jsonrpc_call('ListFolders', [{}] );
          if (!$json) {
                print '<h2>Web services call failed. Error is:</h2> '. $bbauth->{access_credentials_error};
                exit(0);
          }
          # For Yahoo! Photos:
          my $url = 'http://photos.yahooapis.com/V3.0/listAlbums?';
          my $xml = $bbauth->auth_ws_get_call($url);
          if (!$xml) {
              print '<h2>Web services call failed. Error is:</h2> '. $bbauth->{access_credentials_error};
              exit(0);
          }

DESCRIPTION
    This module priovides an Object Oriented interface for Yahoo!
    Browser-Based Authentication.

    This module is ported from the official PHP class which is located on
    this page: http://developer.yahoo.com/php

METHODS
  new(appid => $appid, secret => $secret)
    Returns an instance of this module. You must set the your application id
    and shared secret.

  auth_url(%param)
    Create the Login URL used to fetch authentication credentials. This is
    the first step in the browser authentication process.

    You can set the %param to send_userhash and appdata if you
    need(optional).

    The appdata typically a session id that Yahoo will transfer to the
    target application upon successful authentication.

    If send_userhash set, the send_userhash=1 request will be appended to
    the request URL so that the userhash will be returned by Yahoo! after
    successful authentication.

  validate_sig
    Validates the signature returned by Yahoo's browser authentication
    services.

    Returns false if the sig is invalid. Returns 0 if any error occurs. If 0
    is returned, $self->sig_validation_error should contain a string
    describing the error.

  auth_ws_get_call($url)
    Make an authenticated web services call using HTTP GET. Returns response
    if successful, a string is returned containing the web service response
    which might be XML, JSON, or some other type of text. If an error
    occurs, 0 is returned, and the error is stored in
    $self->access_credentials_error.

  auth_ws_post_call($url)
    Make an authenticated web services call using HTTP POST.

  make_jsonrpc_call($method, $params)
    Make an authenticated web services JSON-RPC call.

  sig_validation_error
    The error message when validate_sig fails.

  access_credentials_error
    The error message when auth_ws_get_call or auth_ws_post_call fail.

ACCESSORS
    appid
    secret
    userhash
    appdata
    timeout
    token
    WSSID
    cookie

AUTHORS
      Jiro Nishiguchi E<lt>jiro@cpan.orgE<gt>
      Jason Levitt E<lt>jlevitt@yahoo-inc.comE<gt>

    This library is free software; you can redistribute it and/or modify it
    under the same terms as Perl itself.

SEE ALSO
    *   http://developer.yahoo.com/auth/

