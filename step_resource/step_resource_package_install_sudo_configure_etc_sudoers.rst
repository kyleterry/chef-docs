.. This is an included how-to. 

The following example shows how to install |sudo cmd| and then configure the ``/etc/sudoers`` file:

.. code-block:: ruby

   #  the following code sample comes from the ``default`` recipe in the ``sudo`` cookbook: https://github.com/opscode-cookbooks/sudo

   package 'sudo' do
     action :install
   end
   
   if node['authorization']['sudo']['include_sudoers_d']
     directory '/etc/sudoers.d' do
       mode        "0755"
       owner       "root"
       group       "root"
       action      :create
     end
   
     cookbook_file '/etc/sudoers.d/README' do
       source      "README"
       mode        "0440"
       owner       "root"
       group       "root"
       action      :create
     end
   end
   
   template '/etc/sudoers' do
     source 'sudoers.erb'
     mode "0440"
     owner "root"
     group platform?('freebsd') ? 'wheel' : 'root'
     variables(
       :sudoers_groups => node['authorization']['sudo']['groups'],
       :sudoers_users => node['authorization']['sudo']['users'],
       :passwordless => node['authorization']['sudo']['passwordless']
     )
   end

where 

* the |resource package| resource is used to install |sudo cmd|
* the ``if`` statement is used to ensure availability of the ``/etc/sudoers.d`` directory
* the |resource template| resource tells the |chef client| where to find the ``sudoers`` template
* the ``variables`` attribute is a hash that passes values to template files (that are located in the ``templates/`` directory for the cookbook

