Metasploit是一款开源的安全漏洞检测工具，可以帮助安全和IT专业人士识别安全性问题，验证漏洞的缓解措施，并管理专家驱动的安全性进行评估，提供真正的安全风险情报。

metasploit提供了很全面的api接口，主要可以分为如下八个方面：

    auth 认证相关，用于远程连接 msf 时的认证
    console 终端相关，用于创建与销毁终端以及与终端的命令交互
    core 内核相关，用于全局性的一些设置
    db 数据库相关，用于存储与查询使用过程中的各种数据
    job 任务相关，对任务的开启与停止或查询当前任务信息
    module 模块相关，对msf各模块信息的查询与配置
    plugin 插件相关，对相关插件的管理
    session 会话相关，对会话信息的管理

由于接口函数数量比较多，放在一篇文章里会略显臃肿，因此我按照上面八个模块将其分开如下几篇文章中进行叙述：
```
    auth
    console
    core
    db
    job
    module
    plugin
    session
```

- auth

auth部分的API函数都是与认证相关的，主要用于远程连接msf时进行认证。

auth.login_noauth  
auth.logout  
auth.token_list  
auth.token_add    
auth.token_generate  
auth.token_remove  

***auth.login_noauth***

    rpc.call(‘auth.login_noauth’, username, password)
    Handles client authentication. The authentication token will expire 5 minutes after the last request was made.

    @param [String] user The username.
    @param [String] pass The password.
    @raise [Msf::RPC::Exception] Something is wrong while authenticating, you can possibly get：
    　　401 Failed authentication.
    @return [Hash] A hash indicating a successful login, it contains the following keys:
    　　‘result’ [String] A successful message: ‘success’.
    　　‘token’ [String] A token for the authentication.
    @example Here is how you would use this from the client:
    　　rpc.call('auth.login_noauth', 'msf', 'msf123')
    This returns something like the following:
    　　{"result"=>"success", "token"=>"TEMPyp1N40NK8GM0Tx7A87E6Neak2tVJ"}
    
***auth.logout***

    rpc.call(‘auth.logout’, token)
    Handles client deauthentication.

    @param [String] token The user’s token to log off.
    @raise [Msf::RPC::Exception] An error indicating a failed deauthentication, including:
    　　500 Invalid authentication token.
    　　500 Permanent authentication token.
    @return [Hash] A hash indiciating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('auth.logout', 'TEMPyp1N40NK8GM0Tx7A87E6Neak2tVJ')
    This returns something like:
    　　{"result"=>"success"}


***auth.token_list***

    rpc.call(‘auth.token_list’)
    temporary, permanent, or stored in the backend.

    @return [Hash] A hash that contains a list of authentication tokens. It contains the following key:
    　　‘tokens’ [Array] An array of tokens.
    @example Here is how you would use this from the client:
    　　rpc.call('auth.token_list')
    This returns something like:
    　　{"tokens"=>["TEMPf5I4Ec8cBEKVD8D7xtIbTXWoKapP", "TEMPtcVmMld8w74zo0CYeosM3iXW0nJz"]}


***auth.token_add***

    rpc.call(‘auth.token_add’, token)
    Adds a new token to the database.

    @param [String] token A unique token.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('auth.token_add', 'UNIQUE_TOKEN')
    This returns something like:
    　　{"result"=>"success"}
***auth.token_generate***

    rpc.call(‘auth.token_generate’)
    database as a side-effect.

    @return [Hash] A hash indicating the action was successful, also the new token.
    It contains the following keys:
    　　‘result’ [String] The successful message: ‘success’
    　　‘token’ [String] A new token.
    @example Here is how you would use this from the client:
    　　rpc.call('auth.token_generate')
    This returns something like:
    　　{"result"=>"success", "token"=>"TEMPyp1N40NK8GM0Tx7A87E6Neak2tVJ"}

***auth.token_remove***

    rpc.call(‘auth.token_remove’, token)
    can remove tokens stored in the database backend (Mdm).

    @param [String] token The token to delete.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('auth.token_remove', 'TEMPtcVmMld8w74zo0CYeosM3iXW0nJz')
    This returns something like:
    　　{"result"=>"success"}

- console

console部分是与终端交互相关的接口函数，是用的比较多的部分。

console.create

console.list

console.destroy

console.read

console.write

console.tabs

console.session_kill

console.session_detach

***console.create***

    rpc.call(‘console.create’)
    Creates a new framework console instance.

    @param [Hash] opts See Msf::Ui::Web::Driver#create_console
    @return [Hash] Information about the new console. It contains the following keys:
    　　‘id’ [Fixnum] The console’s ID.
    　　‘prompt’ [String] The framework prompt (example: ‘msf > ‘)
    　　‘busy’ [TrueClass] The console’s busy state, or
    　　‘busy’ [FalseClass] The console’s busy state.
    @example Here is how you would use this from the client:
    　　rpc.call('console.create')
    This returns something like:
    　　{'busy': False, 'prompt': 'msf > ', 'id': '1'}
***console.list*** 

    rpc.call(‘console.list’)
    Returns a list of framework consoles.

    @return [Hash] Console information.
    　　‘consoles’ [Array] consoles, each element is a hash that includes:
    　　　　‘id’ [Fixnum] The console’s ID
    　　　　‘prompt’ [String] The framework prompt (example: ‘msf > ‘)
    　　　　‘busy’ [TrueClass] The console’s busy state, or
    　　　　‘busy’ [FalseClass] The console’s busy state.
    @example Here is how you would use this from the client:
    　　rpc.call('console.list')
    This returns something like:
    　　{'consoles': [{'busy': False, 'prompt': 'msf > ', 'id': '4'}]}

***console.destroy***

    rpc.call(‘console.destroy’, id)
    Deletes a framework console instance.

    @param [Fixnum] cid Framework console ID.
    @return [Hash] A result indicating whether the action was successful or not. It contains the following key:
    　　‘result’ [String] Either ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　rpc.call('console.destroy', 1)
    This returns something like:
    　　{"result"=>"success"}
***console.read***

    rpc.call(‘console.read’, id)
    Returns the framework console output in raw form.

    @param [Fixnum] cid Framework console ID.
    @return [Hash] There are two different hashes you might get:
    　　If the console ID is invalid, you will get a hash like the following:
    　　　　‘result’ [String] A value that says ‘failure’.
    　　If the console ID is valid, you will get a hash like the following:
    　　　　‘data’ [String] The output the framework console produces (example: the banner)
    　　　　‘prompt’ [String] The framework prompt (example: ‘msf > ‘)
    　　　　‘busy’ [TrueClass] The console’s busy state, or
    　　　　‘busy’ [FalseClass] The console’s busy state.
    @example Here is how you would use this from the client:
    　　rpc.call('console.read', 1)
***console.write***

    rpc.call(‘console.write’, id, input)
    Sends an input (such as a command) to the framework console.

    @param [Fixnum] cid Framework console ID.
    @param [String] data User input.
    @return [Hash] There are two different hashes you might get:
    　　If the console ID is invalid, you will get a hash like the following:
    　　　　‘result’ [String] A value that says ‘failure’.
    　　If the console ID is invalid, you will get a hash like the following:
    　　　　‘wrote’ [Fixnum] Number of bytes sent.
    @note Remember to add a newline (\r\n) at the end of input, otherwise the console will not do anything. And you will need to use the rpc_read method to retrieve the output again.
    @example Here is how you would use this from the client:
    This will show the current module’s options.
    　　rpc.call('console.write', 4, "show options\r\n")
    
***console.tabs***

    rpc.call(‘console.tabs’, id, command)
    Returns the tab-completed version of your input (such as a module path).

    @param [Fixnum] cid Framework console ID.
    @param [String] line Command.
    @return [Hash] There are two different hashes you might get:
    　　If the console ID is invalid, you will get a hash like the following:
    　　　　‘result’ [String] A value that says ‘failure’.
    　　If the console ID is valid, you will get a hash like the following:
    　　　　‘tabs’ [String] The tab-completed version of the command.
    @example Here is how you would use this from the client:
    　　rpc.call('console.tabs', 4, "use exploit/windows/smb/ms08_067_")
    This will return:
    　　{"tabs"=>["use exploit/windows/smb/ms08_067_netapi"]}
***console.session_kill***

    rpc.call(‘console.session_kill’, id)
    You might also want to considering using the session API calls instead of this.

***console.session_detach*** 

    rpc.call(‘console.session_detach’, id)
    background an interactive session.

    @param [Fixnum] cid Framework console ID.
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says ‘success’ if the console ID is valid (and successfully detached, otherwise ‘failed’)
    @example Here is how you would use this from the client:
    　　rpc.call('console.session_detach', 4)

- core

core部分的API主要是用于全局性的一些设置。

core.version 

core.stop
 
core.getg
 
core.setg

core.unsetg

core.save

core.reload_modules

core.add_module_path

core.module_stats

core.thread_list

core.thread_kill

***core.version***

    rpc.call(‘core.version’)
    Returns the RPC service versions.

    @return [Hash] A hash that includes the version information:
    　　‘version’ [String] Framework version
    　　‘ruby’ [String] Ruby version
    　　‘api’ [String] API version
    @example Here is how you would use this from the client:
    　　rpc.call('core.version')
***core.stop*** 

    rpc.call(‘core.stop’)
    Stops the RPC service.

    @return [void]
    @example Here is how you would use this from the client:
    　　rpc.call('core.stop')
    
***core.getg*** 

    rpc.call(‘core.getg’, GlobalSetting)
    Returns a global datastore option.

    @param [String] var The name of the global datastore.
    @return [Hash] The global datastore option. If the option is not set, then the value is empty.
    @example Here is how you would use this from the client:
    　　rpc.call('core.getg', 'GlobalSetting')
    
***core.setg***

    rpc.call(‘core.setg’, globalOpton, globalOptionValue)
    Sets a global datastore option.

    @param [String] var The hash key of the global datastore option.
    @param [String] val The value of the global datastore option.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('core.setg', 'MyGlobal', 'foobar')

***core.unsetg***

    rpc.call(‘core.unsetg’, globalOption)
    Unsets a global datastore option.

    @param [String] var The global datastore option.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('core.unsetg', 'MyGlobal')

***core.save***

    rpc.call(‘core.save’)
    Saves current framework settings.

    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] The successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('core.save')

***core.reload_modules***

    rpc.call(‘core.reload_modules’)
    Reloads framework modules. This will take some time to complete.

    @return [Hash] Module stats that contain the following keys:
    　　‘exploits’ [Fixnum] The number of exploits reloaded.
    　　‘auxiliary’ [Fixnum] The number of auxiliary modules reloaded.
    　　‘post’ [Fixnum] The number of post modules reloaded.
    　　‘encoders’ [Fixnum] The number of encoders reloaded.
    　　‘nops’ [Fixnum] The number of NOP modules reloaded.
    　　‘payloads’ [Fixnum] The number of payloads reloaded.
    @example Here is how you would use this from the client:
    　　rpc.call('core.reload_modules')

***core.add_module_path***

    rpc.call(‘core.add_module_path’, path)
    this will not unload modules that were deleted from the file system that were previously loaded.

    @param [String] path The new path to load.
    @return [Hash] Module stats that contain the following keys:
    　　‘exploits’ [Fixnum] The number of exploits loaded.
    　　‘auxiliary’ [Fixnum] The number of auxiliary modules loaded.
    　　‘post’ [Fixnum] The number of post modules loaded.
    　　‘encoders’ [Fixnum] The number of encoders loaded.
    　　‘nops’ [Fixnum] The number of NOP modules loaded.
    　　‘payloads’ [Fixnum] The number of payloads loaded.
    @example Here is how you would use this from the client:
    　　rpc.call('core.add_module_path', '/tmp/modules/')

***core.module_stats***

    rpc.call(‘core.module_stats’)
    Returns the module stats.

    @return [Hash] Module stats that contain the following keys:
    　　‘exploits’ [Fixnum] The number of exploits.
    　　‘auxiliary’ [Fixnum] The number of auxiliary modules.
    　　‘post’ [Fixnum] The number of post modules.
    　　‘encoders’ [Fixnum] The number of encoders.
    　　‘nops’ [Fixnum] The number of NOP modules.
    　　‘payloads’ [Fixnum] The number of payloads.
    @example Here is how you would use this from the client:
    　　rpc.call('core.module_stats')

***core.thread_list***

    rpc.call(‘core.thread_list’)
    Returns a list of framework threads.

    @return [Hash] A collection of threads. Each key is the thread ID, and the value is another hash that contains the following:
    　　‘status’ [String] Thread status.
    　　‘critical’ [Boolean] Thread is critical.
    　　‘name’ [String] Thread name.
    　　‘started’ [String] Timestamp of when the thread started.
    @example Here is how you would use this from the cient:
    　　rpc.call('core.thread_list')
    You will get something like this:
    　　{0=>{"status"=>"sleep", "critical"=>false, "name"=>"StreamServerListener", "started"=>"2015-04-21 15:25:49 -0500"}}

***core.thread_kill***

    rpc.call(‘core.thread_kill’, id)
    Kills a framework thread.

    @param [Fixnum] tid The thread ID to kill.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] A successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('core.thread_kill', 10)

- db

db部分的API与数据库相关，用于存储与查询使用过程中的各种数据。

db.create_cracked_credential  
db.create_credential  
db.invalidate_login  
db.creds  
db.hosts  
db.services  
db.vulns  
db.workspaces  
db.current_workspace  
db.get_workspace   
db.set_workspace   
db.wspace  
db.get_host  
db.report_host  
db.report_service  
db.get_service  
db.get_note  
db.get_client  
db.report_client  
db.report_note  
db.notes  
db.get_ref  
db.del_vuln  
db.del_note  
db.del_service  
db.del_host  
db.report_vuln  
db.events  
db.report_event  
db.report_loot  
db.loots  
db.import_data  
db.get_vuln  
db.clients  
db.del_client  
db.driver  
db.connect  
db.status  
db.disconnect  

***db.create_cracked_credential***

    rpc.call(‘db.create_cracked_credential’, opts)
    Creates a cracked credential.

    @param [Hash] xopts Credential options. (See #create_credential Documentation)
    @return [Metasploit::Credential::Core]
    @see https://github.com/rapid7/metasploit-credential/blob/master/lib/metasploit/credential/creation.rb#L107 #create_credential Documentation.
    @see #rpc_create_credential
    @example Here is how you would use this from the client:
    
    opts = {
        username: username,`
        password: password,
        core_id: core_id
    }
    rpc.call('db.create_cracked_credential', opts)

***db.create_credential***

    rpc.call(‘db.create_credential’, opts)
    Creates a credential.

    @param [Hash] xopts Credential options. (See #create_credential Documentation)
    @return [Hash] Credential data. It contains the following keys:
    　　‘username’ [String] Username saved.
    　　‘private’ [String] Password saved.
    　　‘private_type’ [String] Password type.
    　　‘realm_value’ [String] Realm.
    　　‘realm_key’ [String] Realm key.
    　　‘host’ [String] Host (Only avilable if there’s a last_attempted_at and status)
    　　‘sname’ [String] Service name (only available if there’s a last_attempted_at and status)
    　　‘status’ [Status] Login status (only available if there’s a last_attempted_at and status)
    @see https://github.com/rapid7/metasploit-credential/blob/master/lib/metasploit/credential/creation.rb#L107 #create_credential Documentation.
    @example Here is how you would use this from the client:
    
    opts = {
        origin_type: service,
        address: '192.168.1.100',
        port: 445,
        service_name: 'smb',
        protocol: 'tcp',
        module_fullname: 'auxiliary/scanner/smb/smb_login',
        workspace_id: myworkspace_id,
        private_data: 'password1',
        private_type: password,
        username: 'Administrator'
    }
    rpc.call('db.create_credential', opts)

***db.invalidate_login***

    rpc.call(‘db.invalidate_login’, opts)
    Sets the status of a login credential to a failure.

    @param [Hash] xopts Credential data (See #invalidate_login Documentation)
    @raise [Msf::RPC::Exception] If there’s an option missing.
    @return [void]
    @see https://github.com/rapid7/metasploit-credential/blob/master/lib/metasploit/credential/creation.rb#L492 #invalidate_login Documentation
    @see https://github.com/rapid7/metasploit-model/blob/master/lib/metasploit/model/login/status.rb Status symbols.
    @example Here is how you would use this from the client:
    
    opts = {
        address: '192.168.1.100',
        port: 445,
        protocol: 'tcp',
        public: 'admin',
        private: 'password1',
        status: 'Incorrect'
    }
    rpc.call('db.invalidate_login', opts)

***db.creds***

    rpc.call(‘db.creds’, opts)
    Returns login credentials from a specific workspace.

    @param [Hash] xopts Options:
    @option xopts [String] :workspace Name of the workspace.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] Credentials with the following hash key:
    　　‘creds’ [Array] An array of credentials. Each hash in the array will have the following:
    　　　　‘user’ [String] Username.
    　　　　‘pass’ [String] Password.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘type’ [String] Password type.
    　　　　‘host’ [String] Host.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘sname’ [String] Service name.
    @example Here is how you would use this from the client:
    　　rpc.call('db.creds', {})

***db.hosts***

    rpc.call(‘db.hosts’, opts)
    Returns information about hosts.

    
 ***db.services***
    
    rpc.call(‘db.services’, opts)
    Returns information about services.

    @param [Hash] xopts Options:
    @option xopts [String] :workspace Name of workspace.
    @option xopts [Fixnum] :limit Limit.
    @option xopts [Fixnum] :offset Offset.
    @option xopts [String] :proto Protocol.
    @option xopts [String] :address Address.
    @option xopts [String] :ports Port range.
    @option xopts [String] :names Names (Use ‘,’ as the separator).
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash with the following keys:
    　　‘services’ [Array] In each hash of the array, you will get these keys:
    　　　　‘host’ [String] Host.
    　　　　‘created_at’ [Fixnum] Last created at.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘state’ [String] Service state.
    　　　　‘name’ [String] Service name.
    　　　　‘info’ [String] Additional information about the service.
    @example Here is how you would use this from the client:
    　　rpc.call('db.services', {})

***db.vulns***

    rpc.call(‘db.vulns’, opts)
    Returns information about reported vulnerabilities.
    
    @param [Hash] xopts Options:
    @option xopts [String] :workspace Name of workspace.
    @option xopts [Fixnum] :limit Limit.
    @option xopts [Fixnum] :offset Offset.
    @option xopts [String] :proto Protocol.
    @option xopts [String] :address Address.
    @option xopts [String] :ports Port range.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash with the following key:
    　　‘vulns’ [Array] In each hash of the array, you will get these keys:
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘time’ [Fixnum] Time reported.
    　　　　‘host’ [String] Vulnerable host.
    　　　　‘name’ [String] Exploit that was used.
    　　　　‘refs’ [String] Vulnerability references.
    @example Here is how you would use this from the client:
    　　rpc.call('db.vulns', {})

***db.workspaces***

    rpc.call(‘db.workspaces’)
    Returns information about workspaces.

    @raise [Msf::RPC::Exception] Database not loaded.
    @return [Hash] A hash with the following key:
    　　‘workspaces’ [Array] In each hash of the array, you will get these keys:
    　　　　‘id’ [Fixnum] Workspace ID.
    　　　　‘name’ [String] Workspace name.
    　　　　‘created_at’ [Fixnum] Last created at.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    @example Here is how you would use this from the client:
    　　rpc.call('db.workspaces')

***db.current_workspace***

    rpc.call(‘db.current_workspace’)
    Returns the current workspace.

    @raise [Msf::RPC::Exception] Database not loaded. Try: rpc.call(‘console.create’)
    @return [Hash] A hash with the following keys:
    　　‘workspace’ [String] Workspace name.
    　　‘workspace_id’ [Fixnum] Workspace ID.
    @example Here is how you would use this from the client:
    　　rpc.call('db.current_workspace')

***db.get_workspace***

    rpc.call(‘db.get_workspace’)
    Returns the current workspace.

    @param [String] wspace workspace name.
    @raise [Msf::RPC::Exception] You might get one of the following errors:
    　　500 Database not loaded
    　　500 Invalid workspace
    @return [Hash] A hash with the following key:
    　　‘workspace’ [Array] In each hash of the array, you will get these keys:
    　　　　‘name’ [String] Workspace name.
    　　　　‘id’ [Fixnum] Workspace ID.
    　　　　‘created_at’ [Fixnum] Last created at.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_workspace')

***db.set_workspace***
    
    rpc.call(‘db.set_workspace’, workspace)
    Sets a workspace.

    @param [String] wspace Workspace name.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    　　404 Workspace not found
    @return [Hash] A hash indicating whether the action was successful or not. You will get:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’
    @example Here is how you would use this from the client:
    This will set the current workspace to ‘default’
    　　rpc.call('db.set_workspace', 'default')

***db.wspace***

    rpc.call(‘db.wspace’, workspace)
    Deletes a workspace.

    @param [String] wspace Workspace name.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　404 Workspace not found
    @return [Hash] A hash indicating the action was successful. It contains the following:
    　　‘result’ [String] A message that says ‘success’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.wspace', 'temp_workspace')
    
***db.get_host***
    
    rpc.call(‘db.get_host’, address)
    Returns information about a host.

    @param [Hash] xopts Options (:addr, :address, :host are the same thing, and you only need one):
    @option xopts [String] :addr Host address.
    @option xopts [String] :address Same as :addr.
    @option xopts [String] :host Same as :address.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘host’ [Array] Each hash in the array contains the following:
    　　　　‘created_at’ [Fixnum] Last created at.
    　　　　‘address’ [String] Address.
    　　　　‘mac’ [String] Mac address.
    　　　　‘name’ [String] Host name.
    　　　　‘state’ [String] Host state.
    　　　　‘os_name’ [String] OS name.
    　　　　‘os_flavor’ [String] OS flavor.
    　　　　‘os_sp’ [String] OS service pack.
    　　　　‘os_lang’ [String] OS language.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘purpose’ [String] Purpose. Example: ‘server’.
    　　　　‘info’ [String] Additional information.
    @example Here is how you would use this from the client:
    　　　　rpc.call('db.get_host', {:host => ip})

***db.report_host***

    rpc.call(‘db.report_host’, hostInfo)
    Reports a new host to the database.

    @param [Hash] xopts Information to report about the host. See below:
    @option xopts [String] :host IP address. You msut supply this.
    @option xopts [String] :state One of the Msf::HostState constants. (See Most::HostState Documentation)
    @option xopts [String] :os_name Something like “Windows”, “Linux”, or “Mac OS X”.
    @option xopts [String] :os_flavor Something like “Enterprise”, “Pro”, or “Home”.
    @option xopts [String] :os_sp Something like “SP2”.
    @option xopts [String] :os_lang Something like “English”, “French”, or “en-US”.
    @option xopts [String] :arch one of the ARCH_* constants. (see ARCH Documentation)
    @option xopts [String] :mac Mac address.
    @option xopts [String] :scope Interface identifier for link-local IPv6.
    @option xopts [String] :virtual_host The name of the VM host software, eg “VMWare”, “QEMU”, “Xen”, etc.
    @see https://github.com/rapid7/metasploit-framework/blob/master/lib/msf/core/host_state.rb Most::HostState Documentation.
    @see https://github.com/rapid7/metasploit-framework/blob/master/lib/rex/constants.rb#L66 ARCH Documentation.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating whether the action was successful or not. It contains the following:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_host', {:host => ip})

***db.report_service***

    rpc.call(‘db.report_service’, serviceInfo)
    Reports a service to the database.

    @param [Hash] xopts Information to report about the service. See below:
    @option xopts [String] :host Required. The host where this service is running.
    @option xopts [String] :port Required. The port where this service listens.
    @option xopts [String] :proto Required. The transport layer protocol (e.g. tcp, udp).
    @option xopts [String] :name The application layer protocol (e.g. ssh, mssql, smb).
    @option xopts [String] :sname An alias for the above
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_service', {:host=>ip, :port=>8181, :proto=>'tcp', :name=>'http'})


***db.get_service***

    rpc.call(‘db.get_service’, search)
    Returns information about a service.

    @param [Hash] xopts Filters for the search, see below:
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :proto Protocol.
    @option xopts [Fixnum] :port Port.
    @option xopts [String] :names Service names.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following key:
    　　‘service’ [Array] Each hash in the array contains the following:
    　　　　‘host’ [String] Host address.
    　　　　‘created_at’ [Fixnum] Creation date.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘state’ [String] Service state.
    　　　　‘name’ [String] Service name.
    　　　　‘info’ [String] Additional information.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_service', {:workspace=>'default', :proto=>'tcp', :port=>443})

***db.get_note***

    rpc.call(‘db.get_note’, opts)
    Returns a note.

    @param [Hash] xopts Options.
    @option xopts [String] :addr Host address.
    @option xopts [String] :address Same as :addr.
    @option xopts [String] :host Same as :address.
    @option xopts [String] :proto Protocol.
    @option xopts [Fixnum] :port Port.
    @option xopts [String] :ntype Note type.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘note’ [Array] Each hash in the array contains the following:
    　　　　‘host’ [String] Host.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘created_at’ [Fixnum] Last created at.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘ntype’ [String] Note type.
    　　　　‘data’ [String] Note data.
    　　　　‘critical’ [String] A boolean indicating criticality.
    　　　　‘seen’ [String] A boolean indicating whether the note has been seen before.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_note', {:proto => 'tcp', :port => 80})

***db.get_client***

    rpc.call(‘db.get_client’, opts)
    Returns information about a client connection.

    @param [Hash] xopts Options:
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :ua_string User agent string.
    @option xopts [String] :host Host IP.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the client connection:
    　　‘client’ [Array] Each hash of the array contains the following:
    　　　　‘host’ [String] Host IP.
    　　　　‘created_at’ [Fixnum] Created date.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘ua_string’ [String] User-Agent string.
    　　　　‘ua_name’ [String] User-Agent name.
    　　　　‘ua_ver’ [String] User-Agent version.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_client', {:workspace=>'default', :ua_string=>user_agent, :host=>ip})

***db.report_client***

    rpc.call(‘db.report_client’, clientInfo)
    Reports a client connection.

    @param [Hash] xopts Information about the client.
    @option xopts [String] :ua_string Required. User-Agent string.
    @option xopts [String] :host Required. Host IP.
    @option xopts [String] :ua_name One of the Msf::HttpClients constants. (See Msf::HttpClient Documentation.)
    @option xopts [String] :ua_ver Detected version of the given client.
    @option xopts [String] :campaign An id or Campaign object.
    @see https://github.com/rapid7/metasploit-framework/blob/master/lib/msf/core/constants.rb#L52 Msf::HttpClient Documentation.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_client', {:workspace=>'default', :ua_string=>user_agent, :host=>ip})


***db.report_note***

    rpc.call(‘db.report_note’, noteInfo)
    Reports a note.

    @param [Hash] xopts Information about the note.
    @option xopts [String] :type Required. The type of note, e.g. smb_peer_os.
    @option xopts [String] :workspace The workspace to associate with this note.
    @option xopts [String] :host An IP address or a Host object to associate with this note.
    @option xopts [String] :service A Service object to associate with this note.
    @option xopts [String] :data Whatever it is you’re making a note of.
    @option xopts [Fixnum] :port Along with +:host+ and +:proto+, a service to associate with this note.
    @option xopts [String] :proto Along with +:host+ and +:port+, a service to associate with this note.
    @option xopts [Hash] A hash that contains the following information.
    　　:unique [Boolean] Allow only a single Note per +:host+/+:type+ pair.
    　　:unique_data [Boolean] Like +:uniqe+, but also compare +:data+.
    　　:insert [Boolean] Always insert a new Note even if one with identical values exists.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_note', {:type=>'http_data', :host=>'192.168.1.123', :data=>'data'})

***db.notes***

    rpc.call(‘db.notes’, search)
    Returns notes from the database.

    @param [Hash] xopts Filters for the search. See below:
    @option xopts [String] :address Host address.
    @option xopts [String] :names Names (separated by ‘,’).
    @option xopts [String] :ntype Note type.
    @option xopts [String] :proto Protocol.
    @option xopts [String] :ports Port change.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘notes’ [Array] Each hash in the array contains the following:
    　　　　‘time’ [Fixnum] Creation date.
    　　　　‘host’ [String] Host address.
    　　　　‘service’ [String] Service name or port.
    　　　　‘type’ [String] Host type.
    　　　　‘data’ [String] Host data.
    @example Here is how you would use this from the client:
    　　This gives you all the notes.
    　　rpc.call('db.notes', {})

***db.get_ref***

    rpc.call(‘db.get_ref’, refName)
    Returns an external vulnerability reference.

    @param [String] name Reference name.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [String] Reference.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_ref', ref_name)

***db.del_vuln***

    rpc.call(‘db.del_vuln’, opts)
    Deletes vulnerabilities.

    @param [Hash] xopts Filters that narrow down which vulnerabilities to delete. See below:
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [String] :address Same as :host.
    @option xopts [Array] :addresses Same as :address.
    @option xopts [Fixnum] :port Port.
    @option xopts [String] :proto Protocol.
    @option xopts [String] :name Name of the vulnerability.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’.
    　　‘deleted’ [Array] Each hash in the array contains the following:
    　　　　‘address’ [String] Host address.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘name’ [String] Vulnerability name.
    @example Here is how you would use this from the client:
    　　rpc.call('db.del_vuln', {:host=>ip, :port=>445, :proto=>'tcp'})


***db.del_note***

    rpc.call(‘db.del_note’, opts)
    Deletes notes.

    @param [Hash] xopts Filters to narrow down which notes to delete.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [String] :address Same as :host.
    @option xopts [Array] :addresses Same as :address.
    @option xopts [Fixnum] :port Port.
    @option xopts [String] :proto Protocol.
    @option xopts [String] :ntype Note type.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’.
    　　‘deleted’ [Array] Each hash in the array contains the following:
    　　　　‘address’ [String] Host address.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘ntype’ [String] Note type.
    @example Here is how you would use this from the client:
    　　rpc.call('db.del_note', {:workspace=>'default', :host=>ip, :port=>443, :proto=>'tcp'})


***db.del_service***

    rpc.call(‘db.del_service’, opts)
    Deletes services.

    @param [Hash] xopts Filters to narrow down which services to delete.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [String] :address Same as :host.
    @option xopts [Array] :addresses Host addresses.
    @option xopts [Fixnum] :port Port.
    @option xopts [String] :proto Protocol.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’ or ‘failed’.
    　　‘deleted’ [Array] If result says success, then you will get this key, Each hash in the array contains:
    　　　　‘address’ [String] Host address.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    @example Here is how you would use this from the client:
    　　rpc.call('db.del_service', {:host=>ip})


***db.del_host***

    rpc.call(‘db.del_host’, opts)
    Deletes hosts.

    @param [Hash] xopts Filters to narrow down which hosts to delete.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [String] :address Same as :host.
    @option xopts [Array] :addresses Host addresses.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’.
    　　‘deleted’ [Array] All the deleted hosts.
    @example Here is how you would use this from the client:
    　　rpc.call('db.del_host', {:host=>ip})


***db.report_vuln***

    rpc.call(‘db.report_vuln’, vulInfo)
    Reports a vulnerability.

    @param [Hash] xopts Information about the vulnerability:
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host The host where this vulnerability resides
    @option xopts [String] :name The friendly name for this vulnerability (title).
    @option xopts [String] :info A human readable description of the vuln, free-form text.
    @option xopts [Array] :refs An array of Ref objects or string names of references.
    @option xopts [Hash] :details A hash with :key pointed to a find criteria hash and the rest containing VulnDetail fields.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_vuln', {:host=>ip, :name=>'file upload'})

***db.events***

    rpc.call(‘db.events’, opts)
    Returns framework events.

    @param [Hash] xopts Options:
    @option xopts [String] :workspace Workspace name.
    @option xopts [Fixnum] :limit Limit.
    @option xopts [Fixnum] :offset Offset.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘events’ [Array] Each hash in the array contains the following:
    　　　　‘host’ [String] Host address.
    　　　　‘created_at’ [Fixnum] Creation date.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘name’ [String] Event name.
    　　　　‘critical’ [Boolean] Criticality.
    　　　　‘username’ [String] Username.
    　　　　‘info’ [String] Additional information.
    @example Here is how you would use this from the client:
    　　rpc.call('db.events', {})


***db.report_event***

    rpc.call(‘db.report_event’, eventInfo)
    Reports a framework event.

    @param [Hash] xopts Information about the event.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :username Username.
    @option xopts [String] :host Host address.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash indicating the action was successful. It contains:
    　　‘result’ [String] A message that says ‘success’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_event', {:username => username, :host=>ip})

***db.report_loot***

    rpc.call(‘db.report_loot’, itemInfo)
    Reports a looted item.

    @param [Hash] xopts Information about the looted item.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [Fixnum] :port Port. Should match :service.
    @option xopts [String] :proto Protocol. Should match :service.
    @option xopts [String] :path Required. Path where the item was looted.
    @option xopts [String] :type Loot type.
    @option xopts [String] :ctype Content type.
    @option xopts [String] :name Name.
    @option xopts [String] :info Additional information.
    @option xopts [String] :data Looted data.
    @option xopts [Mdm::Service] :service Service where the data was found.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.report_loot', {})


***db.loots***

    rpc.call(‘db.loots’, opts)
    Returns all the looted items.

    @param [Hash] xopts Filters that narrow down the search:
    @option xopts [Hash] :workspace Workspace name.
    @option xopts [Fixnum] :limit Limit.
    @option xopts [Fixnum] :offset Offset.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘loots’ [Array] Each hash in the array contains the following:
    　　　　‘host’ [String] Host address.
    　　　　‘service’ [String] Service name or port.
    　　　　‘ltype’ [String] Loot type.
    　　　　‘ctype’ [String] Content type.
    　　　　‘data’ [String] Looted data.
    　　　　‘created_at’ [Fixnum] Creation date.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘name’ [String] Name.
    　　　　‘info’ [String] Additional information.
    @example Here is how you would use this from the client:
    　　rpc.call('db.loots', {})

***db.import_data***

    rpc.call(‘db.import_data’, opts)
    Imports file to the database.

    @param [Hash] xopts A hash that contains:
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] ‘data’ Data to import. The method will automatically detect the file type:
    　　:acunetix_xml
    　　:amap_log
    　　:amap_mlog
    　　:appscan_xml
    　　:burp_session_xml
    　　:ci_xml
    　　:foundstone_xml
    　　:fusionvm_xml
    　　:ip360_aspl_xml
    　　:ip360_xml_v3
    　　:ip_list
    　　:libpcap
    　　:mbsa_xml
    　　:msf_cred_dump_zip
    　　:msf_pwdump
    　　:msf_xml
    　　:msf_zip
    　　:nessus_nbe
    　　:nessus_xml
    　　:nessus_xml_v2
    　　:netsparker_xml
    　　:nexpose_rawxml
    　　:nexpose_simplexml
    　　:nikto_xml
    　　:nmap_xml
    　　:openvas_new_xml
    　　:openvas_xml
    　　:outpost24_xml
    　　:qualys_asset_xml
    　　:qualys_scan_xml
    　　:retina_xml
    　　:spiceworks_csv
    　　:wapiti_xml
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that indicates the action was successful. It contains the following:
    　　‘result’ A message that says ‘success’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.import_data', {'data'=>nexpose_scan_results})

***db.get_vuln***

    rpc.call(‘db.get_vuln’, opts)
    Returns vulnerabilities from services or from a host.

    @param [Hash] xopts Filters to narrow down which vulnerabilities to find.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :proto Protocol.
    @option xopts [Fixnum] :port Port.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘vuln’ [Array] Each hash in the array contains the following:
    　　　　‘host’ [String] Host address.
    　　　　‘port’ [Fixnum] Port.
    　　　　‘proto’ [String] Protocol.
    　　　　‘created_at’ [Fixnum] Creation date.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    　　　　‘name’ [String] Vulnerability name.
    　　　　‘info’ [String] Additional information.
    　　　　‘refs’ [Array] Reference names.
    @example Here is how you would use this from the client:
    　　rpc.call('db.get_vuln', {:proto=>'tcp'})


***db.clients***

    rpc.call(‘db.clients’, opts)
    Returns browser clients information.

    @param [Hash] xopts Filters that narrow down the search.
    @option xopts [String] :ua_name User-Agent name.
    @option xopts [String] :ua_ver Browser version.
    @option xopts [Array] :addresses Addresses.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘clients’ [Array] Each hash in the array that contains the following:
    　　　　‘host’ [String] Host address.
    　　　　‘ua_string’ [String] User-agent string.
    　　　　‘ua_name’ [String] Browser name.
    　　　　‘ua_ver’ [String] Browser version.
    　　　　‘created_at’ [Fixnum] Creation date.
    　　　　‘updated_at’ [Fixnum] Last updated at.
    @example Here is how you would use this from the client:
    　　rpc.call('db.clients', {})

***db.del_client***

    rpc.call(‘db.del_client’, opts)
    Deletes browser information from a client.

    @param [Hash] xopts Filters that narrow down what to delete.
    @option xopts [String] :workspace Workspace name.
    @option xopts [String] :host Host address.
    @option xopts [String] :address Same as :host.
    @option xopts [Array] :addresses Same as :address.
    @option xopts [String] :ua_name Browser name.
    @option xopts [String] :ua_ver Browser version.
    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    　　500 Invalid workspace
    @return [Hash] A hash that contains the following:
    　　‘result’ [String] A message that says ‘success’.
    　　‘deleted’ [Array] Each hash in the array contains the following:
    　　　　‘address’ [String] Host address.
    　　　　‘ua_string’ [String] User-Agent string.
    @example Here is how you would use this from the client:
    　　rpc.call('db.del_client', opts)

***db.driver***

    rpc.call(‘db.driver’, name)
    Sets the driver for the database or returns the current one.

    @param [Hash] xopts Options:
    @option [String] :workspace Workspace name.
    @option [String] :driver Driver name. For example: ‘postgresql’. If this option is not set, then the method returns the current one.
    @return [Hash] A hash that contains:
    　　‘result’ [String] Indiciating whether we’ve successfully set the driver or not.
    　　‘driver’ [String] If the :driver option isn’t set, then this returns the current one.
    @example Here is how you would use this from the client:
    　　Sets a driver
    　　rpc.call('db.driver', {:driver=>new_driver})
    　　Returns the current driver
    　　rpc.call('db.driver', {})


***db.connect***

    rpc.call(‘db.connect’, driverName)
    Connects to the database.

    @param [Hash] xopts Options:
    @option xopts [String] :driver Driver name. For example: ‘postgresql’.
    @return [Hash] A hash that indicates whether the action was successful or not.
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.connect', {:driver=>'postgresql'})

***db.status***

    rpc.call(‘db.status’)
    Returns the database status.

    @raise [Msf::RPC::ServerException] You might get one of these errors:
    　　500 ActiveRecord::ConnectionNotEstablished. Try: rpc.call('console.create')
    　　500 Database not loaded. Try: rpc.call('console.create')
    @return [Hash] A hash that contains the following keys:
    　　‘driver’ [String] Name of the database driver.
    　　‘db’ [String] Name of the database.
    @example Here is how you would use this from the client:
    　　rpc.call('db.status')

***db.disconnect***

    rpc.call(‘db.disconnect’)
    Disconnects the database.

    @return [Hash] A hash that indicates whether the action was successful or not. It contains:
    　　‘result’ [String] A message that says either ‘success’ or ‘failed’.
    @example Here is how you would use this from the client:
    　　rpc.call('db.disconnect')

- job

job部分是与任务相关的API，执行对任务的开启与停止或查询当前任务信息。

job.list  
job.stop  
job.info  


***job.list***

    rpc.call(‘job.list’)
    Returns a list of jobs.

    @return [Hash] A list of jobs (IDs and names). Each key is the job ID, and each value is the job name.
    @example Here is how you would use this from the client:
    　　rpc.call('job.list')
    This will return (‘0’ is the job ID):
    　　{"0"=>"Exploit: windows/browser/ms14_064_ole_code_execution"

***job.stop***

    rpc.call(‘job.stop’, id)
    Stops a job.

    @param [Fixnum] jid Job ID.
    @raise [Msf::RPC::Exception] A 500 response indicating an invalid job ID was given.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] A successful message: ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('job.stop', 0)

***job.info***

    rpc.call(‘job.info’, id)
    Returns information about a job.

    @param [Fixnum] jid Job ID.
    @raise [Msf::RPC::Exception] A 500 response indicating an invalid job ID was given.
    @return [Hash] A hash that contains information about the job, such as the following (and maybe more):
    　　‘jid’ [Fixnum] The Job ID.
    　　‘name’ [String] The name of the job.
    　　‘start_time’ [Fixnum] The start time.
    　　‘datastore’ [Hash] Datastore options for the module.
    @example Here is how you would use this from the client:
    　　rpc.call('job.info', 0)

- module

module部分是与metasploit内部模块相关的API，可以对各模块信息进行查询与配置。

module.exploits  
module.auxiliary  
module.payloads  
module.encoders  
module.nops  
module.post  
module.info  
module.compatible_payloads  
module.compatible_sessions  
module.target_compatible_payloads  
module.options  
module.execute  
module.encode_formats  


***module.exploits***

    rpc.call(‘module.exploits’)
    Returns a list of exploit names. The ‘exploit/‘ prefix will not be included.

    @return [Hash] A list of exploit names. It contains the following key:
    　　‘modules’ [Array] Exploit names, for example: [‘windows/wins/ms04_045_wins’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.exploits')

***module.auxiliary***

    rpc.call(‘module.auxiliary’)
    Returns a list of auxiliary module names. The ‘auxiliary/‘ prefix will not be included.

    @return [Hash] A list of auxiliary module names. It contains the following key:
    　　‘modules’ [Array] Auxiliary module names, for example: [‘vsploit/pii/web_pii’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.auxiliary')


***module.payloads***

    rpc.call(‘module.payloads’)
    Returns a list of payload module names. The ‘payload/‘ prefix will not be included.

    @return [Hash] A list of payload module names. It contains the following key:
    　　‘modules’ [Array] Payload module names, for example: [‘windows/x64/shell_reverse_tcp’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.payloads')

***module.encoders***

    rpc.call(‘module.encoders’)
    Returns a list of encoder module names. The ‘encoder/‘ prefix will not be included.

    @return [Hash] A list of encoder module names. It contains the following key:
    　　‘modules’ [Array] Encoder module names, for example: [‘x86/unicode_upper’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.encoders')


***module.nops***

    rpc.call(‘module.nops’)
    Returns a list of NOP module names. The ‘nop/‘ prefix will not be included.

    @return [Hash] A list of NOP module names. It contains the following key:
    　　‘modules’ [Array] NOP module names, for example: [‘x86/single_byte’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.nops')


***module.post***

    rpc.call(‘module.post’)
    Returns a list of post module names. The ‘post/‘ prefix will not be included.

    @return [Hash] A list of post module names. It contains the following key:
    　　‘modules’ [Array] Post module names, for example: [‘windows/wlan/wlan_profile’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.post')


***module.info***

    rpc.call(‘module.info’, ModuleType, ModuleName)
    Returns the metadata for a module.

    @param [String] mtype Module type. Supported types include (case-sensitive):
    　　exploit
    　　auxiliary
    　　post
    　　nop
    　　payload
    @param [String] mname Module name. For example: ‘windows/wlan/wlan_profile’.
    @raise [Msf::RPC::Exception] Module not found (either the wrong type or name).
    @return [Hash] The module’s metadata. The exact keys you will get depends on the module.
    @example Here is how you would use this from the client:
    　　This gives us the metadata of ms08_067_netapi
    　　rpc.call('module.info', 'exploit', 'windows/smb/ms08_067_netapi')


***module.compatible_payloads***

    rpc.call(‘module.compatible_payloads’, ModuleName)
    Returns the compatible payloads for a specific exploit.

    @param [String] mname Exploit module name. For example: ‘windows/smb/ms08_067_netapi’.
    @raise [Msf::RPC::Exception] Module not found (wrong name).
    @return [Hash] The exploit’s compatible payloads. It contains the following key:
    　　‘payloads’ [Array] A list of payloads. For example: [‘generic/custom’]
    @example Here is how you would use this from the client:
    　　rpc.call('module.compatible_payloads', 'windows/smb/ms08_067_netapi')
    
***module.compatible_sessions***
    
    rpc.call(‘module.compatible_sessions’, ModuleName)
    Returns the compatible sessions for a specific post module.

    @param [String] mname Post module name. For example: ‘windows/wlan/wlan_profile’.
    @raise [Msf::RPC::Exception] Module not found (wrong name).
    @return [Hash] The post module’s compatible sessions. It contains the following key:
    　　‘sessions’ [Array] A list of session IDs.
    @example Here is how you would use this from the client:
    　　rpc.call('module.compatible_sessions', 'windows/wlan/wlan_profile')

***module.target_compatible_payloads***
    
    rpc.call(‘module.target_compatible_payloads’, ModuleName, target)
    Returns the compatible target-specific payloads for an exploit.

    @param [String] mname Exploit module name. For example: ‘windows/smb/ms08_067_netapi’
    @param [Fixnum] target A specific target the exploit module provides.
    @raise [Msf::RPC::Exception] Module not found (wrong name).
    @return [Hash] The exploit’s target-specific payloads. It contains the following key:
    　　‘payloads’ [Array] A list of payloads.
    @example Here is how you would use this from the client:
    　　Find all the compatible payloads for target 1 (Windows 2000 Universal)
    　　rpc.call('module.target_compatible_payloads', 'windows/smb/ms08_067_netapi', 1)

***module.options***

    rpc.call(‘module.options’, ModuleType, ModuleName)
    Returns the module’s datastore options.

    @param [String] mtype Module type. Supported types include (case-sensitive):
    　　exploit
    　　auxiliary
    　　post
    　　nop
    　　payload
    @param [String] mname Module name. For example: ‘windows/wlan/wlan_profile’.
    @raise [Msf::RPC::Exception] Module not found (either wrong type or name).
    @return [Hash] The module’s datastore options. This will actually give you each option’s data type, requirement state, basic/advanced type, description, default value, etc.
    @example Here is how you would use this from the client:
    　　rpc.call('module.options', 'exploit', 'windows/smb/ms08_067_netapi')

***module.execute***

    rpc.call(‘module.execute’, ModuleType, ModuleName, opts)
    Executes a module.


***module.options***

    @param [String] mtype Module type. Supported types include (case-sensitive):
    　　exploit
    　　auxiliary
    　　post
    　　payload
    @param [String] mname Module name. For example: ‘windows/smb/ms08_067_netapi’.
    @param [Hash] opts Options for the module (such as datastore options).
    @raise [Msf::RPC::Exception] Module not found (either wrong type or name).
    @note If you get exploit sessions via the RPC service, know that only the RPC clients have access to those sessions. Framework msfconsole will not be able to use or even see these sessions, because it belongs to a different framework instance. However, this restriction does not apply to the database.
    @return [Hash] It contains the following keys:
    　　‘job_id’ [Fixnum] Job ID.
    　　‘uuid’ [String] UUID.
    @example Here is how you would use this from the client:
    Starts a windows/meterpreter/reverse_tcp on port 6669
    
    opts = {'LHOST' => '0.0.0.0', 'LPORT'=>6669, 'PAYLOAD'=>'windows/meterpreter/reverse_tcp'}
    rpc.call('module.execute', 'exploit', 'multi/handler', opts)
    


***module.encode_formats***

    rpc.call(‘module.encode_formats’)
    Returns a list of encoding formats.

    @return [Array] Encoding foramts.
    @example Here is how you would use this from the client:
    　　rpc.call('module.encode_formats')

- plugin

plugin部分是与插件相关的API，可以对相关插件进行管理。

plugin.load  
plugin.unload  
plugin.loaded  
 
***plugin.load***
 
    rpc.call(‘plugin.load’, filename)
    Loads a plugin.

    @param [String] path The plugin filename (without the extension). It will try to find your plugin in either one of these directories:
    　　msf/plugins/
    　　~/.msf4/plugins/
    @param [Hash] xopts Options to pass to the plugin.
    @return [Hash] A hash indicating whether the action was successful or not. It contains the following key:
    　　‘result’ [String] A value that either says ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　Load the nexpose plugin
    　　rpc.call('plugin.load', 'nexpose')


***plugin.unload***

    rpc.call(‘plugin.unload’, filename)
    Unloads a plugin.

    @param [String] name The plugin filename (without the extension). For example: ‘nexpose’.
    @return [Hash] A hash indicating whether the action was successful or not. It contains the following key:
    　　‘result’ [String] A value that either says ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　rpc.call('plugin.unload', 'nexpose')

***plugin.loaded***

    rpc.call(‘plugin.loaded’)
    Returns a list of loaded plugins.

    @return [Hash] All the plugins loaded. It contains the following key:
    　　‘plugins’ [Array] A list of plugin names.
    @example Here is how you would use this from the client:
    　　rpc.call('plugin.loaded')

- session

session部分是与会话相关的API，可以对会话信息进行管理。

session.list  
session.shell_read  
session.shell_write  
session.shell_upgrade  
session.meterpreter_read  
session.ring_read  
session.ring_put  
session.ring_last  
session.ring_clear  
session.meterpreter_write  
session.meterpreter_session_detach  
session.meterpreter_session_kill  
session.meterpreter_tabs  
session.meterpreter_run_single  
session.meterpreter_script  
session.meterpreter_directory_separator  
session.compatible_modules  

***session.list***

    rpc.call(‘session.list’)
    Returns a list of sessions that belong to the framework instance used by the RPC service.

    @return [Hash] Information about sessions. Each key is the session ID, and each value is a hash that contains the following:
    　　‘type’ [String] Payload type. Example: meterpreter.
    　　‘tunnel_local’ [String] Tunnel (where the malicious traffic comes from).
    　　‘tunnel_peer’ [String] Tunnel (local).
    　　‘via_exploit’ [String] Name of the exploit used by the session.
    　　‘desc’ [String] Session description.
    　　‘info’ [String] Session info (most likely the target’s computer name).
    　　‘workspace’ [String] Name of the workspace.
    　　‘session_host’ [String] Session host.
    　　‘session_port’ [Fixnum] Session port.
    　　‘target_host’ [String] Target host.
    　　‘username’ [String] Username.
    　　‘uuid’ [String] UUID.
    　　‘exploit_uuid’ [String] Exploit’s UUID.
    　　‘routes’ [String] Routes.
    　　‘platform’ [String] Platform.
    @example Here is how you would use this from the client:
    　　rpc.call('session.list')

***session.shell_read***

    rpc.call(‘session.shell_read’, id)
    Reads the output of a shell session (such as a command output).

    @note Shell read is now a positon-aware reader of the shell’s associated ring buffer. For more direct control of the pointer into a ring buffer, a client can instead use ring_read, and note the returned sequence number on their own (making multiple views into the same session possible, regardless of position in the stream)
    @see #rpc_ring_read
    @param [Fixnum] sid Session ID.
    @param [Fixnum] ptr Pointer.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    　　500 Session is disconnected
    @return [Hash] It contains the following keys:
    　　‘seq’ [String] Sequence.
    　　‘data’ [String] Read data.
    @example Here is how you would use this from the client:
    　　rpc.call('session.shell_read', 2)

***session.shell_write***

    rpc.call(‘session.shell_write’, id, data)
    You may want to use #rpc_shell_read to retrieve the output.

    @note shell_write is a wrapper of #rpc_ring_put.
    @see #rpc_ring_put
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    　　500 Session is disconnected
    @param [Fixnum] sid Session ID.
    @param [String] data The data to write.
    @return [Hash]
    　　‘write_count’ [Fixnum] Number of bytes written.
    @example Here is how you would use this from the client:
    　　rpc.call('session.shell_write', 2, "DATA")

***session.shell_upgrade***

    rpc.call(‘session.shell_upgrade’, id, LHOST, LPORT)
    Upgrades a shell to a meterpreter.

    @note This uses post/multi/manage/shell_to_meterpreter.
    @param [Fixnum] sid Session ID.
    @param [String] lhost Local host.
    @param [Fixnum] lport Local port.
    @return [Hash] A hash indicating the actioin was successful. It contains the following key:
    　　‘result’ [String] A message that says ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('session.shell_upgrade', 2, payload_lhost, payload_lport)

***session.meterpreter_read***

    rpc.call(‘session.meterpreter_read’, id)
    Reads the output from a meterpreter session (such as a command output).

    @note Multiple concurrent callers writing and reading the same Meterperter session can lead to a conflict, where one caller gets the others output and vice versa. Concurrent access to a Meterpreter session is best handled by post modules.
    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] It contains the following key:
    　　‘data’ [String] Data read.
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_read', 2)

***session.ring_read***

    rpc.call(‘session.ring_read’, id)
    Reads from a session (such as a command output).

    @param [Fixnum] sid Session ID.
    @param [Fixnum] ptr Pointer.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    　　500 Session is disconnected
    @return [Hash] It contains the following key:
    　　‘seq’ [String] Sequence.
    　　‘data’ [String] Read data.
    @example Here is how you would use this from the client:
    　　rpc.call('session.ring_read', 2)

***session.ring_put***

    rpc.call(‘session.ring_put’, id, data)
    Sends an input to a session (such as a command).

    @param [Fixnum] sid Session ID.
    @param [String] data Data to write.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    　　500 Session is disconnected
    @return [Hash] It contains the following key:
    　　‘write_count’ [String] Number of bytes written.
    @example Here is how you would use this from the client:
    　　rpc.call('session.ring_put', 2, "DATA")

***session.ring_last***

    rpc.call(‘session.ring_last’, id)
    Returns the last sequence (last issued ReadPointer) for a shell session.

    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] It contains the following key:
    　　‘seq’ [String] Sequence.
    @example Here is how you would use this from the client:
    　　rpc.call('session.ring_last', 2)


***session.ring_clear***

    rpc.call(‘session.ring_clear’, id)
    Clears a shell session. This may be useful to reclaim memory for idle background sessions.

    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash indicating whether the action was successful or not. It contains:
    　　‘result’ [String] Either ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　rpc.call('session.ring_clear', 2)

***session.meterpreter_write***

    rpc.call(‘session.meterpreter_write’, id, input)
    You may want to use #rpc_meterpreter_read to retrieve the output.

    @note Multiple concurrent callers writing and reading the same Meterperter session can lead to a conflict, where one caller gets the others output and vice versa. Concurrent access to a Meterpreter session is best handled by post modules.
    @param [Fixnum] sid Session ID.
    @param [String] data Input to the meterpreter prompt.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash indicating the action was successful or not. It contains the following key:
    　　‘result’ [String] Either ‘success’ or ‘failure’.
    @see #rpc_meterpreter_run_single
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_write', 2, "sysinfo")

***session.meterpreter_session_detach***

    rpc.call(‘session.meterpreter_session_detach’, id)
    Detaches from a meterpreter session. Serves the same purpose as [CTRL]+[Z].

    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash indicating the action was successful or not. It contains:
    　　‘result’ [String] Either ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_session_detach', 3)

***session.meterpreter_session_kill***

    rpc.call(‘session.meterpreter_session_kill’, id)
    Kills a meterpreter session. Serves the same purpose as [CTRL]+[C].

    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash indicating the action was successful or not. It contains the following key:
    　　‘result’ [String] Either ‘success’ or ‘failure’.
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_session_kill', 3)

***session.meterpreter_tabs***

    rpc.call(‘session.meterpreter_tabs’, id, input)
    Returns a tab-completed version of your meterpreter prompt input.

    @param [Fixnum] sid Session ID.
    @param [String] line Input.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] The tab-completed result. It contains the following key:
    　　‘tabs’ [String] The tab-completed version of your input.
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_tabs', 3, 'sysin')
    This returns:
    　　{"tabs"=>["sysinfo"]}


***session.meterpreter_run_single***

    rpc.call(‘session.meterpreter_run_single’, id, command)
    You will want to use the #rpc_meterpreter_read to retrieve the output.

    @param [Fixnum] sid Session ID.
    @param [String] data Command.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_run_single', 3, 'getpid')


***session.meterpreter_script***

    rpc.call(‘session.meterpreter_script’, id, scriptName)
    Runs a meterpreter script.

    @deprecated Metasploit no longer maintains or accepts meterpreter scripts. Please try to use post modules instead.
    @see Msf::RPC::RPC_Module#rpc_execute You should use Msf::RPC::RPC_Module#rpc_execute instead.
    @param [Fixnum] sid Session ID.
    @param [String] data Meterpreter script name.
    @return [Hash] A hash indicating the action was successful. It contains the following key:
    　　‘result’ [String] ‘success’
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_script', 3, 'checkvm')

***ession.meterpreter_directory_separator***

    rpc.call(‘session.meterpreter_directory_separator’, id)
    Returns the separator used by the meterpreter.

    @param [Fixnum] sid Session ID.
    @raise [Msf::RPC::Exception] An error that could be one of these:
    　　500 Session ID is unknown
    　　500 Invalid session type
    @return [Hash] A hash that contains the separator. It contains the following key:
    　　‘separator’ [String] The separator used by the meterpreter.
    @example Here is how you would use this from the client:
    　　rpc.call('session.meterpreter_directory_separator', 3)
    This returns:
    　　{"separator"=>"\\"}


***session.compatible_modules***

    rpc.call(‘session.compatible_modules’, id)
    Returns all the compatible post modules for this session.

    @param [Fixnum] sid Session ID.
    @return [Hash] Post modules. It contains the following key:
    　　‘modules’ [Array] An array of post module names. Example [‘post/windows/wlan/wlan_profile’]
    @example Here is how you would use this from the client:
    　　rpc.call('session.compatible_modules', 3)
