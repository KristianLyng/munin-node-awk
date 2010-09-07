Munin-node-awk
==============

munin-node-awk is a simple implementation of munin-node, which in turn is a
daemon used on servers (nodes) to query the system for statistics every
time a "munin master" comes asking for it. Typically every 5 minutes.
It is written in awk, with two goals:

1. Play around
2. Prototype the idea of a super-light non-forking munin-node

Usage
-----

If you want it to run in the background, you'll have to figure it out
yourself. (Ie: use normal UNIX-tricks).

::
	
	./munin-node-awk [-v hostname=yourhostname] [-v domain=yourdomain] 
		[-v debug=integer] [-v port=tcpport]

All options are ... optional. The defaults are:

hostname
	uname -n

domain
	".awk" (Since I still haven't added enough plugins to replace the
        real munin-node on my system)

debug
	0

port
	4919 (NB: This is NOT the default munin port. This is for the same
	reason as above: I run munin-node and munin-node-awk in parallel)

cpus
	calculated from /proc/stats and cached. Don't see the point in
	over-riding it, but you're welcome.

Deviations from munin-node
--------------------------

Because the "plugins" are all contained inside the node, they do not need
to support suggest or autoconf or any magic markers. Those are aides for
munin-node, and we don't need them.

munin-node-awk still doesn't support "cap" properly, mostly because there
isn't a need until a plugin needs it.

I also had some issues with hidden protocol details (like the fact that
munin-update expects a banner on connect, even if it has a # at the start.
It simply froze until it got it). The reason for the dot (".") is also
undocumented as far as the protocol goes.

Ideas
-----

Some ideas I am testing: 

- Caching and sharing of relevant variables (ie: number of cpus)
- Virtually fork-free execution. There is some need for forking, but the
  goal is to avoid forking for every query, which is what munin-node does.
- "Wildcard" plugins can probably be expanded
- Parallel connection to stuff like varnish to fetch statistics real time
  without opening a file (not implemented, just and idea right now).

This is thus far a prototype, and the one big missing thing I have is the
lack of "dynamic" plugins. I have to manually add all plugins to the
switch() statements. That might be acceptable, though.

Use case
--------

Two primary use-cases: 

1. Low-end systems, like dd-wrt, where you don't want a big node and a
   large amount of plugins.
2. Overloaded systems, like my Varnish servers when I am doing stress
   testing. Having munin-node fork a quadzillion processes is immensely
   slow if your system already has a load of 300. This could also be
   expanded to do real-time stats.

Credits
-------

Munin: http://www.munin-monitoring.org

Author of munin-node-awk: Kristian Lyngstøl <kristian@bohemians.org>
(See my blog, http://kristianlyng.wordpress.com )

That's about it, I borrowed the cpu stuff from the cpu graph, but as that
is uncredited too, it doesn't really help :)
