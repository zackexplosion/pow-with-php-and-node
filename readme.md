# Serve Ruby on Rails, PHP, and Node with Pow (POW!)

<section class="post-meta"><time class="post-date" datetime="2014-06-05">05 June 2014</time></section></header><section class="post-content">
> Note that Pow runs only on Mac OSX

Here at **Differential**, we work on diverse projects. Serving up Rails apps, PHP apps, and Node apps with zero configuration is the Holy Grail of development environments. Okay, I made that up, but it's pretty darn challenging. Well, this post will not tell you how to do these things with zero configuration, but it gets pretty darn close.

As a reminder, there are two requirements for serving up your awesome web app from your laptop.

1.  DNS – `app.dev`, `project.local`, `dev.test` or whatever domain and TLD you decide to use must resolve to `127.0.0.1`.
2.  Application server – Once the request hits your machine on some port, some application server must route it to your application code.

### Ruby on Rails

I won't go into much detail here as Pow has already figured this one out and is the de facto standard-bearer. So [get Pow, install it, and use it](http://pow.cx) to get a 1-step configuration for new projects. For the record (we'll need this later), that one step is:

<pre class="commentable-section hljs coffeescript" data-section-id="3">% ln -s <span class="hljs-regexp">/path/to/projects/railsapp ~/</span>.pow/railsapp  
</pre>
And `http://railsapp.dev` should load your Rails app.

### PHP

While Pow serves up Rack apps with a custom server, our old friend Apache is still the easiest way to run PHP apps. We'll need to do a few things to get zero-configuration PHP apps running.

1.  Assuming all of our Rails apps have symlinks, we take advantage of Pow's [proxying](http://pow.cx/manual.html#section_2.1.4) and [`default`](http://pow.cx/manual.html#section_2.1.3) virtual host features to proxy any non-Rack app to port 81, where Apache will be listening.

    <pre class="commentable-section hljs ruby" data-section-id="7"><span class="hljs-input"><span class="hljs-prompt">% echo 81 &gt;</span> ~<span class="hljs-regexp">/.pow/default</span></span>  
    </pre>
2.  Now of course we have to configure Apache to listen on port 81\. On Mac, this is in the file `/etc/apache2/httpd.conf` under the `Listen` directive. Change this value and bounce Apache.

3.  We want Apache to serve any PHP folder without having to specify a virtual host each time. Apache has a nifty directive called [`VirtualDocumentRoot`](http://httpd.apache.org/docs/2.2/mod/mod_vhost_alias.html#virtualdocumentroot) which allows host variables in your document roots. Create a `VirtualHost` block that looks something like this somewhere in your Apache config.

    <pre class="commentable-section hljs apache" data-section-id="10"><span class="hljs-tag">&lt;VirtualHost 127.0.0.1:81&gt;</span>  
       <span class="hljs-keyword"><span class="hljs-common">ServerName</span></span> any.dev
       <span class="hljs-keyword">ServerAlias</span> *.dev
       <span class="hljs-keyword">VirtualDocumentRoot</span> <span class="hljs-string">"/path/to/projects/%1"</span>
    <span class="hljs-tag">&lt;/VirtualHost&gt;</span>  
    </pre>

Thanks to Pow proxying and `VirtualDocumentRoot`, simply creating a directory at `/path/to/projects/phpapp` should immediately be available at `http://phpapp.dev`

### Node

Weighing in at two commands, node apps require the most amount of work. We simply proxy Pow requests to whatever port your node app is running on. So if you start your node app on port 3000, then create a proxy file for Pow to port 3000 for your app. Nothing fancy.

<pre class="commentable-section hljs ruby" data-section-id="13"><span class="hljs-input"><span class="hljs-prompt">% echo 3000 &gt;</span> ~<span class="hljs-regexp">/.pow/nodeapp</span></span>  
</pre>
Then `cd` to your node project directory, and start your app however it starts. For example,

<pre class="commentable-section hljs " data-section-id="15">% node app.js  
</pre>
And your node app should be available at `http://nodeapp.dev`. I await a Pow for node.

UPDATE: _Node instructions also apply to Meteor. However in the case of either node or Meteor, Websockets are not proxied, so they will usually fall back to Ajax polling_.

### Using `.local` as a TLD

If you prefer to use `.local` as your development TLD, as I do, then there is a [potential problem](http://superuser.com/questions/370559/10-second-delay-for-local-tld-in-mac-os-x-lion) on Mac as that is the TLD Mac uses for Bonjour. To avoid DNS lookup delays or failures, [run a local DNS nameserver](http://support.apple.com/kb/HT3473). Mac OS ships with such a nameserver in BIND. To enable and configure BIND, read [Setting up wildcard DNS on localhost domains on OSX](http://mikeferrier.com/2011/04/04/setting-up-wildcard-dns-on-localhost-domains-on-osx/). There are some bonuses achieved with running BIND locally, such as setting your upstream DNS servers. For example, you may wish to use [Google's public DNS](https://developers.google.com/speed/public-dns/) instead of your ISP's, who may hijack things you type into your browser's address bar.

### To Sum Up

With this setup, I am able to serve up a new PHP project with zero steps, a new Rails project with 1 step (create symlink), and a new node project with 2 steps (create symlink, start node). Not too bad, eh?

Happy local development!

</section><footer class="post-footer"><figure class="author-image">[<span class="hidden">Gerard Sychay's Picture</span>](/author/hellogerard/)</figure><section class="author">
#### [Gerard Sychay](/author/hellogerard/)

Read [more posts](/author/hellogerard/) by this author.

<div class="author-meta"><span class="author-location icon-location">Beautiful Cincinnati, OH</span></div></section><section class="share">
#### Share this post

[<span class="hidden">Twitter</span>](https://twitter.com/share?text=Serve%20Ruby%20on%20Rails%2C%20PHP%2C%20and%20Node%20with%20Pow%20(POW!)&url=http://blog.differential.com/serve-ruby-on-rails-php-and-node-with-pow-pow/)[<span class="hidden">Facebook</span>](https://www.facebook.com/sharer/sharer.php?u=http://blog.differential.com/serve-ruby-on-rails-php-and-node-with-pow-pow/)[<span class="hidden">Google+</span>](https://plus.google.com/share?url=http://blog.differential.com/serve-ruby-on-rails-php-and-node-with-pow-pow/)</section></footer></article></main><footer class="site-footer clearfix"><section class="copyright">[Differential's Blog](http://blog.differential.com) © 2015</section><section class="poweredby">Proudly published with [Ghost](https://ghost.org)</section></footer></div>![](/view.gif?page=/serve-ruby-on-rails-php-and-node-with-pow-pow/)
