# What documents go where?

The OpenSwitch project uses the Markdown markup language to generate its documentation.
All documentation that is contributed to the project needs to be written in this format so that it can get built into the project.

The following table lists the type of documents, the  target locations and the expected file names. Find below the table a small description of each type of document.

| Doc Type                	| Repo                     	| Directory 	| File Name               	|
|-------------------------	|--------------------------	|-----------	|-------------------------	|
| User Guides            	| openswitch/ops           	| /docs     	| [feature]_user_guide.md 	|
| Command References      	| openswitch/ops           	| /docs     	| [feature]_cli.md        	|
| Feature Designs         	| openswitch/ops           	| /docs     	| [feature]_design.md     	|
| Feature Test Plans      	| openswitch/ops           	| /tests    	| [feature]_test.md       	|
| Component Functionality 	| openswitch/ops-component 	| /         	| README.md               	|
| Component Design        	| openswitch/ops-component 	| /         	| DESIGN.md               	|
| Component Test Plan     	| openswitch/ops-component 	| /tests    	| [component]_test.md       	|

## User Guides
It documents how to use the different OpenSwitch features.
## Command References
It documents the command usage for the different features found in the OpenSwitch project.
## Feature Designs
It provides a high level design description for the feature.
## Feature Test Plans
It contains a high level overview of the test plans for each feature.
## Component Functionality
It describes the content of the component on this repo and provides pointers to other documents.
## Component Design
It describes a high level design of the component.
## Component Test Plan
It contains all the component tests documentation relevant to the specific repository.
