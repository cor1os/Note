# Postgrepsql For Metesploit

初始化数据库`msfdb init`

报错

```
/opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/activesupport-4.2.10/lib/active_support/core_ext/kernel/agnostics.rb:7:in ``': Cannot allocate memory - infocmp (Errno::ENOMEM)
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/activesupport-4.2.10/lib/active_support/core_ext/kernel/agnostics.rb:7:in ``'
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/rb-readline-0.5.5/lib/rbreadline.rb:1815:in `get_term_capabilities'
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/rb-readline-0.5.5/lib/rbreadline.rb:2027:in `_rl_init_terminal_io'
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/rb-readline-0.5.5/lib/rbreadline.rb:2564:in `readline_initialize_everything'
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/rb-readline-0.5.5/lib/rbreadline.rb:3849:in `rl_initialize'
	from /opt/metasploit-framework/embedded/lib/ruby/gems/2.4.0/gems/rb-readline-0.5.5/lib/rbreadline.rb:4868:in `readline'
	from /opt/metasploit-framework/embedded/framework/lib/rex/ui/text/input/readline.rb:162:in `readline_with_output'
	from /opt/metasploit-framework/embedded/framework/lib/rex/ui/text/input/readline.rb:100:in `pgets'
	from /opt/metasploit-framework/embedded/framework/lib/rex/ui/text/shell.rb:375:in `get_input_line'
	from /opt/metasploit-framework/embedded/framework/lib/rex/ui/text/shell.rb:191:in `run'
	from /opt/metasploit-framework/embedded/framework/lib/metasploit/framework/command/console.rb:48:in `start'
	from /opt/metasploit-framework/embedded/framework/lib/metasploit/framework/command/base.rb:82:in `start'
	from /opt/metasploit-framework/bin/../embedded/framework/msfconsole:49:in `<main>'
```