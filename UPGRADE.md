## Production Upgrades:

When Blacksmith was released, the upgrade strategy was the migration upgrade:
  1. Ensure you have a backup of the old service.
  2. Spin up a newer instance of the service.
  3. Migrate the data from the old service to the new service.
  4. Test the new service with the target app
  5. Replace the old service with the new service

This is recommended for all production upgrades as the data is safe if any issues are encountered, the rollback strategy is to remain on or switch back to the original service. Issues could include lack of disk space, interrupted upgrades, or incompatibilities in the newer version, among others.  

For dev and staging, a full migration upgrade can be daunting.  We are adding an upgrade command to Blacksmith, which uses automation (in this case BOSH's workflow) to upgrade the stemcell and/or the release.  The updated forges will be able to detect the change in versions and run a forge provided migration script if needed.



## Forge Authors:

How to write a "Upgrade" script for release upgrades.


# There are two places upgrades happen.

The first is on the Blacksmith service broker with the Vault.  Instead of the "init" script creating values, the "upgrade/check" script run and verifies all the values are present.  If any value needed for the upgraded service is missing, the script can set a default value or return a failure code.

The second is on the actual service instance while the manifest is being deployed.  The monit script will stop the old service.  The new packages will be installed and monit will start the service.  When starting the service, the script can check if the service was on an older version and run any require migration or upgrade steps needed.  The release can be written to use Monit alone or wrap the service in BOSH prestart (https://bosh.io/docs/pre-start/ and https://github.com/cloudfoundry/exemplar-release/blob/master/README.md#pre-start-docs)


