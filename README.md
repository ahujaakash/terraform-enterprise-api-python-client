## Pyterprise

This is an object oriented client API for Terraform Enterprise written in Python.
The methods included in this library generally map 1 to 1 in terms of function naming conventions to 
[terraform enterprise documentation](https://www.terraform.io/docs/cloud/api/), so please review the available methods 
if you are uncertain on this library's usage.

Happy Terraforming!


### Installation:
This module can be installed via pip, this library is compatible with `python3.7`. Install it with pip for python3.

`pip3 install --user pyterprise`

### Usage:

*Note: Some examples are covered here, but more are covered in the 
[examples directory for more basic use-cases of this api.](https://github.com/JFryy/terraform-enterprise-api-python-client/tree/master/examples)*

First import the module and authenticate using the init method, you can retrieve a token from the terraform enterprise UI.
```python
import pyterprise

tfe_token = 'TOKENHERE'
client = pyterprise.Client()

# Supply your token as a parameter and the url for the terraform enterprise server.
# If you are not self hosting, use the one provided by hashicorp.
client.init(token=tfe_token, url='https://example-host.com')
```


After instantiating your client you need to set an organization to access workspaces and more. 
From the client base-level you can also create/destroy/list organizations:

```python
# Set the organization
org = client.set_organization(id='my-organization')

# Base level Methods:
#organizations = client.list_organizations()
#client.destroy_organization(name='my-safe-to-delete-org')
#client.create_organization(name='test', email='test@test.com')

```


This organization object `org` has access to workspaces, ssh keys and other methods (Check source code/examples for more):
```python
# Creating a workspace with VCS example:
vcs_options = {
    "identifier": "my-github-org/my-repo",
    "oauth-token-id": "ot-xxxxxxxxxxx",
    "branch": "master",
    "default-branch": False
}
org.create_workspace(name='test-delete-me2',
                     vcs_repo=vcs_options,
                     auto_apply=False,
                     queue_all_runs=False,
                     working_directory='/',
                     trigger_prefixes=['/'])
                              
# Or Delete a workspace at the organization level by name.
org.delete_workspace(name='my-safe-to-delete-test-workspace')
```


You can access workspace objects through your organization client object, 
which gives you access to runs, plans, statefiles, ssh-key additions and variables.
```python
# Get a single workspace client object, 
# you can also list or search all workspace objects using the corresponding methods.
workspace = org.get_workspace('test-workspace')

# Get a list of all workspace objects in an organization.
for workspace in org.list_workspaces():
    print(workspace)

# Print some workspace attributes. Additionally you can access all attributes 
# as a dictionary by printing  the 'workspace' object at top level.                     
print(workspace.name, workspace.id, workspace.created_at)

# Run Workspace, you can destroy by changing the destroy flag.
workspace.run(destroy_flag=False)

# Plan and Apply in workspace with log output
workspace.plan_apply(destroy_flag=False)

# Update workspace, arguments identical to create function. Omit arguments to not alter.
workspace.update(auto_apply=True)

# Create a variable in a workspace
workspace.create_variable(key='foo', value='bar', sensitive=False, category='env')

# Print all variables for a workspace. 
# Variables objects also have functions for updating and deleting.
for variable in workspace.list_variables():
    print(variable)
    

# Apply latest unconfirmed plan in workspace. You can additionally print all attributes by print object at top level.
# or use other runs methods i.e. cancel, discard and get plan output etc.
for run in workspace.list_runs(page=1, page_size=20):
    if run.status == 'planned':
        run.apply(comment='Running latest plan.')
```

Please consult module contents, [examples](https://github.com/JFryy/terraform-enterprise-api-python-client/tree/master/examples) 
or [terraform enterprise api documentation](https://www.terraform.io/docs/cloud/api/)
for more api client usage. This API does not currently cover administrative functions and teams, 
help is wanted for expanding functionality. Thank you.


### API Coverage/Helper Methods

##### [Workspaces](https://www.terraform.io/docs/enterprise/api/workspaces.html)
- [x] Method Coverage Complete.

##### [Runs](https://www.terraform.io/docs/enterprise/api/run.html)
- [x] Method Coverage Complete

##### [Plans](https://www.terraform.io/docs/cloud/api/plans.html)
- [x] Method Coverage Complete

##### [Organization](https://www.terraform.io/docs/cloud/api/organizations.html)
- [x] Method Coverage Complete

##### [SSH-Keys](https://www.terraform.io/docs/cloud/api/ssh-keys.html)
- [x] Method Coverage Complete

##### [Variables](https://www.terraform.io/docs/enterprise/api/variables.html)
- [x] Method Coverage Complete

