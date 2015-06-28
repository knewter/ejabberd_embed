EjabberdEmbed
=============

This is a repo to reproduce some issues I have with ejabberd lately as it
relates to embedding in an elixir application.

First off, if you clone this repo and just `mix deps.get; mix test` you'll run
into an issue as some of the dependencies reference http for their git
dependencies, and some reference git.  Mix doesn't know these are the same, so
you have to override.  You get the following error:

```
[jadams:~/elixir/ejabberd_embed] 1m31s $ mix test
Unchecked dependencies for environment test:
* p1_utils (https://github.com/processone/p1_utils)
  the dependency p1_utils in deps/ejabberd/rebar.config is overriding a child dependency:

  > In deps/ejabberd/rebar.config:
    {:p1_utils, ~r/.*/, [git: "https://github.com/processone/p1_utils"]}

  > In deps/p1_cache_tab/rebar.config:
    {:p1_utils, ~r/.*/, [git: "git://github.com/processone/p1_utils.git"]}

  Ensure they match or specify one of the above in your EjabberdEmbed.Mixfile deps and set `override: true`
* p1_tls (https://github.com/processone/tls)
  the dependency p1_tls in deps/ejabberd/rebar.config is overriding a child dependency:

  > In deps/ejabberd/rebar.config:
    {:p1_tls, ~r/.*/, [git: "https://github.com/processone/tls"]}

  > In deps/esip/rebar.config:
    {:p1_tls, ~r/.*/, [git: "git://github.com/processone/tls.git"]}

  Ensure they match or specify one of the above in your EjabberdEmbed.Mixfile deps and set `override: true`
* p1_stun (https://github.com/processone/stun)
  the dependency p1_stun in deps/ejabberd/rebar.config is overriding a child dependency:

  > In deps/ejabberd/rebar.config:
    {:p1_stun, ~r/.*/, [git: "https://github.com/processone/stun"]}

  > In deps/esip/rebar.config:
    {:p1_stun, ~r/.*/, [git: "git://github.com/processone/stun.git"]}

  Ensure they match or specify one of the above in your EjabberdEmbed.Mixfile deps and set `override: true`
** (Mix) Can't continue due to errors on dependencies
```

If you then add the needed overrides:

```elixir
  defp deps do
    [
      {:ejabberd, github: "processone/ejabberd"},
      {:p1_utils, github: "processone/p1_utils", override: true},
      {:p1_tls, github: "processone/tls", override: true},
      {:p1_stun, github: "processone/stun", override: true}
    ]
  end
```

...then try to run the tests, it will fail with no ejabberd.app:

```
[jadams:~/elixir/ejabberd_embed] 24s $ mix test
==> rel (compile)
==> ejabberd (compile)
Unchecked dependencies for environment test:
* ejabberd (git://github.com/processone/ejabberd.git)
  could not find an app file at _build/test/lib/ejabberd/ebin/ejabberd.app. This may happen if the dependency was not yet compiled, or you specified the wrong application name in your deps, or the dependency indeed has no app file (then you can pass app: false as option)
** (Mix) Can't continue due to errors on dependencies
```
