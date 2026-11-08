# IKE Verification

The supplied pre-Group-19 output showed the IKE Security Association in state `UP` at both endpoints using IKEv1 Main Mode.

- HQ remote peer: `10.10.40.1`
- Branch remote peer: `10.10.30.1`

Both peers were subsequently changed from DH Group 5 to Group 19. The supplied SA output predates that change. Capture fresh post-change SA detail output to verify negotiated parameters.
