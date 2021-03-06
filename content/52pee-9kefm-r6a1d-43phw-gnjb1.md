# The Modern Personal Computer

One thing I've been noticing lately is how much the PICO-8 (and, to a lesser extent, the Pocket C.H.I.P.) is reflecting something deep for me. I'm constantly noticing things that I've been struggling to express that the P8 seems to just *understand*. Here's a big one (and I'll fully admit this is somewhat ahistoric, but it leads into the concept I'm going for):

In the early days, to use a computer and to program it were the same. If you owned a computer, you had all the tools you needed to build something for it: it was the machine's native mode. So much of the design of Unix (like "usr" containing system assets, or the config directory being called "etc") comes from "that's just how Ken had it set up on his machine, and it stuck".

But we've made it all increasingly inaccessible. Not just ivory towers: ivory *cathedrals* - an entire Byzantine empire that gives you the web apps it tells you you want, and if you have a better idea, well, maybe *you* can start a million-dollar server farm and compete. Or you can use ours - on our terms, at our prices, in a vendor-locked form that will ensnare you so that your work can never escape...

The base of the kateskit design is to push back against the notion that you should need an elaborate and expensive operations center to get reliable, works-anywhere service. But that only covers half the ground: users should also be able to *change* the stuff they're running just was easily as running it. The kateskit needs an editor that's as simple as the PICO-8 to fire off a minor tweak, and then see it live within seconds.

## original rant starts here

So, the idea here is, someone could say "I want to get into app development like the pros", so they can buy an off-the-shelf product (like a Raspberry Pi with the SD card pre-flashed and inserted) that comes pre-loaded running a full-device minikube cluster (and a wifi hotspot for the initial configuration) that's running *everything* they'll need to start building actual, scalable, best-practices platform apps:

- All the normal Minikube addons
- A user-management server like [Accouch](3bgmz-ptkas-baa2b-w9a4z-kmm7f) that provides OIDC/OAuth so it can be used to authorize users to Kubernetes, but also can be configured as the backing store
  - And it can be fully designed to just expect one user logging in, ie. its initial Login UI after setup is configured to work like a desktop OS, where the last user to log in is pre-selected and their avatar is displayed
  - And note this can use a factor like outside services, or Yubico, or passwords, or Face ID or whatever the user feels most comfortable with
  - So maybe one of the aspects of this'd be that it has a "pre-configured own-app-id-provisioning wizard" deal, where each device is pre-registered in a way that it can be recognized as an API consumer with GitHub / Facebook / whatever - point is, we make it as easy as possible to introduce to those services, in a way where we maybe even take responsibility for it while the user decides who's going to be responsible for maintaining the integration
- a GitLab or Gogs or whatever instance (using said backing store)
- Some component that can be easily set up (maybe as part of the repo browser app?) to create a Heroku-style deployment process for a Git app
  - This should be as something *outside* the codebase - I'm not talking about some circle.ci spec type thing here.
- A portal that can provision workspaces (ie. [wayside](46qjk-agdzr-a58ez-vypmf-3hxay))
  - As I learn k8s, I'm thinking each of these workspaces'd probably be a Helm release or whatever you call it when you install an app from Helm
- Are projects namespaces in k8s? Can maybe a project have multiple namespaces (ie prod, test, this-team, that-team, where each team uses their own instance of CI or whatever)
- A ["Make Your New Namespace" Wizard](0d8cc-k29em-qw93j-mj01y-dkf5a), that would take your logo, suggest a bunch of apps to install, pre-configure them all with your name and logo, set up integrations to outside services (GitHub, Facebook, Twitter, Google Webmaster Tools, possible hosting providers for the cluster to expand to, etc), set everything up to give access to the users in your namespace... etc
  - As noted above, I mean "namespace" here as "entity that would have its own instances of a bunch of apps", but there might be further levels of "namespace" under this where the "project wizard
  - thoughts on how you'd want workspace access/provisioning to work tracking under [wayside](46qjk-agdzr-a58ez-vypmf-3hxay)
  - There are levels to this around dev infrastructure that... I guess would be prompts in something that initializes a Helm chart for "your app"? Which could be a whole different kind of app

## Key feature missing from the dev ecosystem for this to focus on

This should be about making it easy to start up a project, have a canonical "reference operation" (like a live reference implementation), and the dev pipeline (from IDE to repo / project-managment to CI). Maybe also the "publish to the cloud repo" step, too.

The idea would be that, for non-web-apps, you could knock off the CI part of this and just have it federate out to whatever outside publisher (Play / Apple App Store, Chrome / Firefox Extensions, GNOME Extensions, Facebook Apps, GitHub Integrations, Thingiverse / PrusaPrinters, whatever) by changing that (but you'd probably just want to add it and keep the CI for hosting a webpage for the project by its own domain/subdomain)

## Ingress and NAT

Note that a lot of this overlaps Sandstorm's [Sandcats](https://github.com/sandstorm-io/sandcats)

This is one of the key selling points of this: the critical thing that makes the "web interface" label *seamless* is being able to access your devices *via a global domain name*. You can set up your network so they're only accessible on your local network (and external access requires authorizing the tunnel)

So, for hooking these up to the outside world, I'm thinking the default to a vendor-supplied namespace, and can maybe be "ordered" (ie. configured before first boot) with a given name pre-loaded (including bringing one's own domain), so maybe the Vendor provides DNS where you have a complimentary domain (renamable, I'm thinking) for each device (like under the ".mac.com" domain back in Apple's heyday), as well as some custom domains (though that might be a paid subscription feature if it's too hard to maintain)

but since this'd probably need to point to a fixed IP and we don't know if we want to do DDNS shenanigans and all that other home-ingress setup on the user's router, we have some kind of NAT-punching tunnel run from the device to Ingress servers (this might require some real fancy footwork wrt Kubernetes). This might also be a subscription feature (with some amount of free trial included with the device)

This is actually an interesting concept: the tunnel can check if you're "on the same network" as an authorization level when connecting to the device, and then NAT punching

Maybe this can also be a matter if you set the device up to do DNS for all systems on your network, ie. the suggested service over DHCP

I think this is one of the reasons I originally though this should be a specialized router in the CanBee stages of this idea. Not to mention it'd have unique insight on what traffic to route to services

Maybe there's a way to interface Kubernetes to consumer routers for configuring this kind of integration (so they don't have to be sharing resources on the same device, where a Minecraft game logic bug could slow network traffic)? Maybe if they're reflashed with a custom firmware?

Hell, for that matter: can client DHCP leases be integrated into Kubernetes' networking? Or is that traditionally just a "configure them to not overlap" deal?

## More thoughts around domain

This oughta have some kind of [Parked Project](gefm7-awg6f-0qa19-h93mk-42q9m) app easy to install

## App model

So, this aspect of this is kind of like how Sandstorm intended to operate

I'm envisioning a key component to this being a Helm repo maintained for each of these apps that are featured for this platform, and stable and the others can be easily enabled (with the caveat that the Vendor does not provide support for these)

These are kind of like Fabric8's "Integrations", though one of the key distinctions is that it's totally designed to be *all about* the "integrations" - users might even just buy it for game server instance provisioning, or real-time-chat service (like, they want something to replace an IRC server that's retiring), or decentralized service experimentation (hello, DatBoy 3000), or 3D printing (ie. the component apps of [printacle](v62a9-2ccas-m5a21-pppj8-966e8))

so really the only "core" components over Kubernetes' core stuff are the Helm stuff, and the dev-centric model I described above is secondary (and the key point is that any of these can have other kinds of app installed onto them to become the other - cluster roles are fluid)

so maybe that's what it's called. Helmy.

INTERJECTION: Wait, what am I talking about here? Is this KubeApps? It sounds like I'm just reinventing KubeApps - maybe a "social" interface around them, that makes the KubeApps expereience more like a Play Store / marketplace?

Too bad we can't sell the default case as a Benchy, though maybe a deal could be brokered for that

## Scaling

The classic use case for this: you want a home server, and you want an office server, and you want to load balance so that, if your home internet connection goes down, your office can still access the app.

another use case where one device splits to two (and a much easier to work with outscaling): migration. You've been running multiple types of server on one node (ie. both Coding and Gaming), and you want to split the machines so they have different responsibilities (and maybe they leave the cluster at some point)

This might be another reason to have tunnels to an ingress point / load balancer as a service with this

## Non-dedicated-device version

You know, it might make sense to have this as an app people can install on their home PCs that runs a minikube VM thing - the selling point being "synchronize your cloud, so your favorite service will NEVER go down or change, because you're running it"

and of course the key point is that you still get the easy hookup to an Internet domain that lets you access all this from your phone and Chromebook and everything

the big enhancement over the separate-device design would just be making it so the DNS is designed to connect to the local replica if the Internet connection for it fails, so you're not dependent on the Vendor Load Balancer

and this'd probably be implemented by "change your DNS from 1.1.1.1 or 8.8.8.8 to 10.10.10.10", though idk

## Coupons?

Some ideas:

- Some kind of deal where all this can integrate with other Kubernetes services, for migrating to the cloud if your home system ends up getting not how you want it

## ethic

note that all this would be build on SACRED principles
