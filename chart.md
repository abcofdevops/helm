# Chart.yaml

To understand the components of chart.yaml please read the comments before each line.

>chart.yaml
```bash
#Helm chart API version. Use v2 for Helm 3+.
apiVersion: v2

#Name of the chart (should match the folder name).
name: service1
description: A Helm chart for Kubernetes


# A chart can be either an 'application' or a 'library' chart. 
# application (default) or library (used for reusable charts).
#
# application → standard app deployment (default)
# library → reusable template helpers (cannot be deployed directly)
type: application

# Chart version. Follows semver and used by Helm to track upgrades.
version: 0.1.0

# The version of the application the chart deploys. Used only for display, not for logic.
appVersion: "1.16.0"

# For importing any template/common charts or any dependencies stored as package. 
dependencies:
    - name: common-chart
      repository: <Link of Repository>
      version: 0.1.0
```