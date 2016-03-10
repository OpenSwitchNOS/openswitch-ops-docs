# Plugin Extensions Design

## Contents
- [Plugin Infrastructure Requirements](#plugin-infrastructure-requirements)
- [Types of SwitchD Plugins](#types-of-switchd-plugins)
- [Feature Plugins](#feature-plugins)
	- [Feature Plugins Interface](#feature-plugins-interface)
	- [Adding a New Function to Plugin Interface](#adding-a-new-function-to-plugin-interface)
	- [Versioning Example](#versioning-example)
	- [Client Code Versioning Rules](#client-code-versioning-rules)
	- [Plugins Extensions API](#plugins-extensions-api)
		- [Register Plugin Extension](#register-plugin-extension)
		- [Unregister Plugin Extension](#unregister-plugin-extension)
		- [Find Plugin Extension](#find-plugin-extension)
- [ASIC Plugins](#asic-plugins)
- [Plugin Initialization](#plugin-initialization)
	- [Plugin Initialization Configuration File](#plugin-initialization-configuration-file)
- [Reconfigure Blocks](#reconfigure-blocks)
	- [Reconfigure Blocks API](#reconfigure-blocks-api)
		- [Register Reconfigure Callback](#register-reconfigure-callback)
		- [Execute Reconfigure Block](#execute-reconfigure-block)
	- [Reconfigure Blocks Data Structures](#reconfigure-blocks-data-structures)


## Plugin Infrastructure Requirements
<p align="justify">
- Simplify Feature Development by enabling features to be implemented as plugins for switchd instead of modifying the switchd code to add all the features and code to enable/disable features at build time.<br><br>
- Avoid modifying the main SwitchD bridge code to add new features.<br><br>
- Create the infrastructure for feature plugins to share and export code that could be used by other plugins and also by the main switchd code base.<br><br>
- Extend the existing ASIC plugin infrastructure to support multi-vendor ASIC plugins and specify ASIC plugin interface versioning requirements for the main switchd code base to set the minimum interface version that must be implemented by an ASIC plugin to be safely used by the SwitchD code base. Versioning also guards against ABI breakage between the main Switchd code and the ASIC plugin used (when different versions of switchd and the ASIC plugins are used).<br><br>
- Provide a versioning method to be able to validate the plugin interface version used by the plugin and the client code to guard against ABI breakage caused by mixing different versions of switchd code and plugins.<br><br>
- Provide infrastructure that allows to specify via a YAML configuration file the plugin initialization order required on a per platform level. Any plugins not listed in the YAML configuration file will be initialized last.<br><br>
- Plugins must be able to support Phased intialization, where the init call may get executed multiple times but with a different phase id number so the init process can support several stages.<br><br>
- Provide infrastructure for feature plugins to be able to register callback handlers at different points in the bridge reconfigure event, so that plugins can register themselves, listen in on changes and make their own feature dependent changes at different stages of the reconfigure event.<br><br>
- The plugin infrastructure must support both the new feature plugin model as well as the current hard coded features in SwitchD model (backward compatible - eventually existing features could be migrated to the new feature plugin infrastructure if desired).<br><br>
- Feature plugins can live in the same repo as the SwitchD code base or on separate repos. Feature plugins can be built outside of SwitchD (but are dependent on SwitchD headers and OVS libraries).<br><br>
- Allows platforms to pick and choose which features are shipped with the code by selecting which feature plugins are included in the product filesystem.<br>
</p>

## Types of SwitchD Plugins
There are two types of SwitchD Plugins: feature plugins and asic plugins.

## Feature Plugins
Are plugins that implement the code of a Feature or they export some common functionality that may be used by other plugins (like utility plugins). A feature plugin may implement:

- The Platform Independent code (PI) of the feature
- The Platform Dependent code (PD) of the feature, but relies on the Platform ASIC plugin to drive the changes to the ASICs
- Both PI and PD code of a feature, but still relies on the Platform ASIC plugin to drive the changes to the ASICs

### Feature Plugins Interface
A feature plugin must export the following Interface structure:

```
struct plugin_extension_interface {
    const char* plugin_name; /**< Key for the hash interface */
    int major; /**< Major number to check plugins version */
    int minor; /**< Minor number to check plugins version */
    void *plugin_interface; /**< Start of exported plugin functions */
};
```
Feature Plugins are registered and looked up by their defined plugin_name string. They must provide a Major and a Minor number. These Major and Minor numbers will be used to validate the proper versioning when a client request to find the interface for a given version of the plugin name.

The plugin_interface is a void pointer that points to a feature plugin interface which holds all the function pointers (exported functions).

### Adding a New Function to Plugin Interface
- No Fields can be inserted in between existing fields (no reordering of public functions)
- Any new fields (public functions) must be added to the end of the interface header and this will trigger an increase in the Interface Minor number.
- If any fields change their parameters then this will trigger an increase in the Interface Major number.

### Versioning Example
Feature Plugins implement and enforce Versioning from the Feature Plugin Public Interface into the Client Code that wants to use the Plugin's interface:

- Feature Plugin Registers itself with a Major number 2 and minor number 3 when the plugin is initialized
- When Client Code requests to find the plugin interface it must provide the plugin name, the Major and Minor numbers that were used when the code was compiled
- So if the Client code was compiled with version 2.3 of the Plugin Interface it will pass the versioning check be able to use the plugin interface
- If the Client code was compiled with version 2.1 of the Plugin Interface it will fail the versioning check and not be able to use the plugin interface


### Client Code Versioning Rules

- Client code must be compiled with the Major Number as the Plugin Interface Major number. MAJOR ==
- Client code can be compiled with equal or larger Minor Number as the Plugin Interface Minor number. MINOR >=
- The usage of the Client Versioning is transparent as it is a result of compiling the code using the public interface header of the used Feature plugin.


### Plugins Extensions API
Features plugins use the Plugins Extensions API to register and share their public functionality:

```
int register_plugin_extension (struct plugin_extension_interface *new_extension);

int unregister_plugin_extension (const char *plugin_name);

int find_plugin_extension (const char *plugin_name, int major, int minor, struct plugin_extension_interface **plugin_interface);
```

* #### Register Plugin Extension
<p align="justify">
Takes a pointer to the plugin interface structure to register as a pointer. The plugin infrastructure will use the plugin_name, Major and Minor numbers from the structure to register the interface. Returns 0 for a successful registration and <0 for any errors
</p>

* #### Unregister Plugin Extension
<p align="justify">
Takes the name of the plugin and searches the registered plugin interfaces, if found it will remove the interface and return 0. Returns <0 on any errors or if the plugin interface is not found
</p>

* #### Find Plugin Extension
<p align="justify">
Takes the plugin name, major and minor numbers used when the client code was compiled. It will look for the given plugin by name, if found it will compare the registered plugin interface Major and Minor numbers against the client code Major and Minor numbers and will validate that the client code can use the registered plugin interface. If the plugin is found and the version validation is successful the function returns 0 and the pointer to the plugin interface is stored in the plugin_interface parameter. It returns <0 on any errors, if the Version Validation fails or if the plugin is not found.
</p>

## ASIC Plugins
<p align="justify">
Are plugins that implement the platform dependent asic code. There can be only one asic plugin loaded in the system. The asic plugin must implement all the functionality defined in asic_plugin.h. The plugin infrastructure will enforce that the asic plugin meets the major and minor versioning numbers specified in the asic_plugin.h file to guard against ABI breakage. For asic plugins, versioning is enforced by the plugin infrastructure based on the exported **plugin interface**.

An ASIC plugin MUST implement the interface defined in asic_plugin.h as the plugin_interface pointer in the plugin_extension_interface structure (see [here](#feature-plugins-interface) ). For version 1.1 of the ASIC plugin interface the structure is:

</p>

```
struct asic_plugin_interface {
    int (*set_port_qos_cfg)(struct ofproto *ofproto,
    void *aux,
    const struct qos_port_settings *settings);
    int (*set_cos_map)(struct ofproto *ofproto,
    const void *aux,
    const struct cos_map_settings *settings);
    int (*set_dscp_map)(struct ofproto *ofproto,
    void *aux,
    const struct dscp_map_settings *settings);
};

Note: You can export more functions as needed.
```
The ASIC plugin uses the same API from the feature plugins to register and find extension.


## Plugin Initialization
<p align="justify">
One of the requirements is to be able to specify via a YAML configuration file the order in which all the plugins in the system are initialized. Also it must support phased initialization, in which the init function for a plugin may be executed several times, but each time with a different phase id number, so the plugins may implement different stages of initialization that may depend on being interlaced with other plugin initialization.

All plugins must provide an init function with the following prototype:

</p>

```
void init (int phase_id);
```

### Plugin Initialization Configuration File
<p align="justify">
The plugin initialization configuration file is called plugins.yaml and is located in the platform default configuration file path. The format uses the plugin base file name (without the .so.XXX)  to identify each plugin:
</p>

```
plugins.yaml:

- libswitchd_asic_vendorA_plugin

- libswitchd_feature1_plugin

- libswitchd_feature2_plugin

- libswitchd_feature1_plugin

- libswitchd_feature3_plugin

- libswitchd_feature2_plugin

...
```

<p align="justify">
* If a plugin name is listed several times then it will be executed multiple times in a phased initialization, each time with an increasing phase_id number.
* If a plugin is not listed in the yaml configuration file it will be added at the end of the init sequence and called only once.
* If no plugin yaml configuration file is found in the filesystem then plugins will be initialized in the order in which they are discovered in the filesystem and called only once.
</p>

## Reconfigure Blocks
<p align="justify">
Reconfigure Blocks allows an external SwitchD plugin to register callback handlers to be triggered at several different points in the reconfigure bridge event. This enables the external plugin to be able to listen and make changes at different points in the bridge reconfigure logic.

Once a change in the switch configuration is detected (by a change in the OVSDB sequence number), the Bridge reconfigure function flow can be broken down in the following segments:
</p>

```
    Update Bridge and VRF ofproto data structures, nothing is pushed down the ofproto layer
    <RECONFIGURE ENTRY POINT BLK_INIT_RECONFIGURE>
    For each bridge delete ports
    <RECONFIGURE ENTRY POINT BLK_BR_DELETE_PORTS>
    For each Vrf delete ports
    <RECONFIGURE ENTRY POINT BLK_VRF_DELETE_PORTS>
    Applies delete changes to ofproto layer
    For each bridge delete or reconfigure ports
    <RECONFIGURE ENTRY POINT BLK_BR_RECONFIGURE_PORTS>
    For each vrf delete or reconfigure ports
    <RECONFIGURE ENTRY POINT BLK_VRF_RECONFIGURE_PORTS>
    Create and push new bridge and vrf ofproto objects to ofproto layer
    For each bridge add new ports
    <RECONFIGURE ENTRY POINT BLK_BR_ADD_PORTS>
    For each bridge add new ports
    <RECONFIGURE ENTRY POINT BLK_VRF_ADD_PORTS>
    Configure features like vlans, mac_table
    <RECONFIGURE ENTRY POINT BLK_BR_FEATURE_RECONFIG>
    For each configured port in a vrf add neighbors
    <RECONFIGURE ENTRY POINT BLK_VRF_ADD_NEIGHBORS>
    For each vrf reconfigure neighbors and reconfigure routes
    <RECONFIGURE ENTRY POINT BLK_VRF_RECONFIGURE_NEIGHBORS>
```


The callback handler will receive a blk_params struct as a parameter which holds references to the IDL handler and the Ofproto handler.


### Reconfigure Blocks API

```
int register_reconfigure_callback(void (*callback_handler)(struct blk_params*), enum block_id blk_id, unsigned int priority);

int execute_reconfigure_block(struct blk_params *params, enum block_id blk_id);
```

* #### Register Reconfigure Callback
<p align="justify">
Registers a plugin callback handler into a specified block. This function receives a priority level that is used to execute all registered callbacks in a block in an ascending order (NO_PRIORITY can be used when ordering is not important or needed).
</p>

* #### Execute Reconfigure Block
<p align="justify">
Executes all registered callbacks on the given block_id with the given block parameters.
</p>

### Reconfigure Blocks Data Structures

```
enum {
   BLK_INIT_RECONFIGURE = 0,
   BLK_BR_DELETE_PORTS,
   BLK_VRF_DELETE_PORTS,
   BLK_BR_RECONFIGURE_PORTS,
   BLK_VRF_RECONFIGURE_PORTS,
   BLK_BR_ADD_PORTS,
   BLK_VRF_ADD_PORTS,
   BLK_BR_PORT_UPDATE,
   BLK_BR_FEATURE_RECONFIG,
   BLK_VRF_PORT_UPDATE,
   BLK_VRF_ADD_NEIGHBORS,
   BLK_VRF_RECONFIGURE_NEIGHBORS,
   /* Add more blocks here*/

   /* MAX_BLOCKS_NUM marks the end of the list of reconfigure blocks.
    * Do not add other reconfigure blocks ids after this. */
   MAX_BLOCKS_NUM,
};
```
```
struct blk_params{
   unsigned int idl_seqno;   /* Current transaction's sequence number */
   struct ovsdb_idl *idl; /* OVSDB IDL handler */
   struct ofproto *ofproto; /* Ofproto handler */
   struct bridge *br;        /* Reference to current bridge. Only valid for
                                reconfigure blocks parsing bridge instances */
   struct vrf *vrf;          /* Reference to current vrf. Only valid for
                                reconfigure blocks parsing vrf instances */
   struct port *port;        /* Reference to current port. Only valid for
                                reconfigure blocks parsing port instances */
   struct hmap *all_bridges; /* Map containing all bridge intances */
   struct hmap *all_vrfs;    /* Map containing all vrf instances*/
};
```
