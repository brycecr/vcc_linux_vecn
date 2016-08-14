# vCC Virtual ECN Linux PoC Implementation

### Description

This is a linux proof-of-concept system for the
Virtualized Congestion Control system proposed in
our paper [available at http://dl.acm.org/citation.cfm?id=2934889&CFID=653697983&CFTOKEN=87338481 ].
This patch is in the linux tcp stack itself and so
is not in a hypervisor, as true vCC is. However,
we have attempted to structure this patch such
that it is "hypervisor-like" in the sense that
the patch sits in the tcp input path at one
location and modifies packets. There is a single
entry point and a single exit point to the patch,
in other words, and the location is chosen such that
a virtual switch filter could modify packets in the
same way.
For convenience, this patch does take advantage of
some of the exisiting state and packet processing
in the tcp stack, but all of the facilities used
would be straightforward to replicate in a hypervisor
implementation and, in some cases, are in our ESXi
vCC implementation.

The intention of this patch is to provide a proof-of-concept
for vCC using the receive window throttling technique in
order provide a ECN-aware underlay congestion control
algorithm to a guest using an ECN-unaware congestion
control algorithm. Note that, in this case, the guest
congestion control is not actually ECN-unaware, but
we simulate that for the purposes of this patch.

This patch provides receive-window-throttling-based ECN
response to an ECN-unaware guest, starting with the first
ECN seen and staying in congestion avoidance mode thereafter.
As such, this patch does not include connection setup, slow start,
fast-retransmit, or timeout handling; it's focus is on steady-state
ECN response.

### Using this patch

This is a patch on linux kernel version 3.19, which
can be found by tag, release, or hash
bfa76d49576599a4b9f9b7a71f23d73d6dcff735 at
https://github.com/torvalds/linux . The release page, from which
the source code can be downloaded as an archive, is available
here: https://github.com/torvalds/linux/releases/tag/v3.19

To use this patch, clone torvalids/linux and
check out kernel version 3.19. Then apply
this patch (perhaps using `git apply`),
and build
(make; sudo make modules_install; sudo make install).

The experiments to demonstrate the capabilities of this
patch and re-create the results from the paper can be
found at https://github.com/aranb/vcc-exp

The homepage for vCC, with links to relevant repositories
and the latest extended version of the paper, can be found here:
http://webee.technion.ac.il/~isaac/vcc/
