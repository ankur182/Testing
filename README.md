# Testing
The tests were not removed arbitrarily.
Main implementing site validation is no longer common across assets:
aircraft and vessel have different valid implementing sites and different control behaviour.
Keeping these scenarios in AssetMonitoringIntegrationTest would require asset-specific if/else logic inside a shared integration test, which would reduce readability and maintainability.
To reflect the domain rules more accurately, the tests were split and adapted into:
AircraftMonitoringIntegrationTest
VesselMonitoringIntegrationTest
Each test class now validates the correct behaviour for its asset type without conditional branching.
