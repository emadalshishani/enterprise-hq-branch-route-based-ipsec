# IPsec Verification

The supplied pre-Group-19 output showed one active tunnel / one IPsec SA on both peers. The displayed algorithm was `ESP:aes-cbc-128/sha1`.

Both endpoints were subsequently changed to PFS Group 19. The captured SA output predates this update and is not evidence of the post-change negotiated PFS setting.

The configured IPsec integrity algorithm remains HMAC-SHA1-96 in the supplied configuration.
