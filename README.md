Ansible Playbook: oracle-apply-patches
======================================

Apply one-off and quarterly patches to Grid Infrastructure and/or Database Oracle Homes (both PRIMARY and STANDBY).

See `oracle-manage-patches` role for detailed description.

This playbook expects the following variables to be set (use `--extra-vars` - see examples below):

* `oracle_manage_patches_patch_type` - either 'oneoff' or any 'oracle_manage_patches_quarterly_patches' key (i.e. ojvmgicombo/ojvmdbcombo/ru/dbbp/psu)
* `oracle_manage_patches_patch_name` - patch name (e.g. OCT2018/JUL2018) for quarterly patches


Additionally, the playbook:
- disables all preconfigured cron-jobs (`oracle_apply_patches_manage_cron_jobs`=true) before applying the patch
- sets downtime mode for Nagios monitoring checks (`oracle_apply_patches_manage_monitoring`=true + `oracle_apply_patches_downtime_duration`) before applying the patch
- and reverses those changes after applying the patch


#### Note:

> By default this playbook runs in a single-host mode to protect from its accidental execution across the whole landscape.

This just means, that it will fail whenever a number of inventory hostnames that are active in the current play is greater that 1. 
One may disable this behaviour with `oracle_apply_patches_single_host_mode` variable set to `false`.


Supported OS:
-------------
* RedHat
* CentOS
* OracleLinux

Requirements
------------

This playbook requires the following roles:
* `oracle`
* `oracle-gatherinfo-gi`
* `oracle-gatherinfo-databases`
* `oracle-gatherinfo-dbconsole`
* `oracle-gatherinfo-listener`
* `nacl-manage-checks`
* `oracle-asm-metadata`
* `oracle-homes-backup`
* `oracle-host-cron`
* `oracle-download-patches`
* `oracle-manage-patches`


`$ ansible-galaxy install -r roles/requirements.yml`

Examples
--------

    $ ansible-playbook playbook.yml --list-tasks
    $ ansible-playbook playbook.yml --list-tags

DRY RUN (check mode) - run all prechecks, opatch conflict detection, etc. before applying OCT2018 Combo of OJVM component DB PSU + GI PSU patch on oradbserver.foo.com. This mode will NOT stop any service or databases. This should be triggered before real patch mode to verify all prerequisites are met.

    $ ansible-playbook playbook.yml --extra-vars='{oracle_manage_patches_patch_type: ojvmgicombo, oracle_manage_patches_patch_name: OCT2018}' --limit=oradbserver.foo.com --check

Execute the playbook in Dry Run (check mode) and, additionally, backup affected ORACLE_HOMEs.

    $ ansible-playbook playbook.yml --extra-vars='{oracle_manage_patches_patch_type: ojvmgicombo, oracle_manage_patches_patch_name: OCT2018, oracle_manage_patches_backup_oracle_home: true}' --limit=oradbserver.foo.com --check
	
Apply OCT2018 Combo of OJVM component DB PSU + GI PSU patch on oradbserver.foo.com

    $ ansible-playbook playbook.yml --extra-vars='{oracle_manage_patches_patch_type: ojvmgicombo, oracle_manage_patches_patch_name: OCT2018}' --limit=oradbserver.foo.com
	
Disable single-host mode and apply OCT2018 across the landscape

    $ ansible-playbook playbook.yml --extra-vars='{oracle_apply_patches_single_host_mode: false, oracle_manage_patches_patch_type: ojvmgicombo, oracle_manage_patches_patch_name: OCT2018}'

Apply the patch (Combo of OJVM component + DB PSU) on oradbserver.foo.com to 11.2.0.3.0 and 11.2.0.4.0 Homes only (narrow down the scope of the affected ORACLE_HOMEs)

    $ ansible-playbook playbook.yml --extra-vars='{oracle_manage_patches_patch_type: ojvmdbcombo, oracle_manage_patches_patch_name: OCT2018, oracle_manage_patches_oracle_home_version_patterns: [ 11.2.0.3.0, 11.2.0.4.0 ] }' --limit=oradbserver.foo.com
	
Apply the patch (Combo of OJVM component + DB PSU) on oradbserver.foo.com to selected ORACLE_HOMEs only

    $ ansible-playbook playbook.yml --extra-vars='{oracle_manage_patches_patch_type: ojvmdbcombo, oracle_manage_patches_patch_name: OCT2018, oracle_manage_patches_oracle_home_name_patterns: [ /u01/app/oracle/product/11.2.0/dbhome_1, /u01/app/oracle/product/11.2.0/dbhome_2 ] }' --limit=oradbserver.foo.com
	
	
License
-------

GPLv3 - GNU General Public License v3.0

Author Information
------------------

This playbook was created in 2018 by [Krzysztof Lewandowski](mailto:Krzysztof.Lewandowski@fastmail.fm).


