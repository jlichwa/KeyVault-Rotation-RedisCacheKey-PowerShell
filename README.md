# KeyVault-Rotation-RedisCacheKey-PowerShell
##Key Vault Redis Cache Key Rotation Functions

Functions regenerate individual key (alternating Primary and Secondary) in Redis Cache and add regenerated key to Key Vault as new version of the same secret.

Functions require following information stored in secret as tags:
- $secret.Tags["ValidityPeriodDays"] - number of days, it defines expiration date for new secret
- $secret.Tags["CredentialId"] - key id (key1 or key2)
- $secret.Tags["ProviderAddress"] - Redis Cache Resource Id

You can create new secret with above tags and Redis Cache key as value or add those tags to existing secret. For automated rotation expiry date would also be required - it triggers event 30 days before expiry

There are two available functions performing same rotation:
- AKVRedisRotation - event triggered function, performs Redis Cache key rotation triggered by Key Vault events. In this setup Near Expiry event is used which is published 30 days before expiration
- AKVRedisRotationHttp - on-demand function with KeyVaultName and Secret name as parameters

## Rotation Setup - ARM Templates

There are 3 ARM templates available
- [Initial Setup](https://github.com/jlichwa/KeyVault-Rotation-RedisKey-PowerShell/tree/master/arm-templates#inital-setup)- Creates Key Vault and Redis Cache if needed. Existing Key Vault and Redis Cache can be used instead
- [Function Rotation - Complete Setup](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell/tree/master/arm-templates#redis-cache-key-rotation-functions) - It creates and deploys function app and function code, creates necessary permissions, and Key 
Vault event subscription for Near Expiry Event for individual secret (secret name can be provided as parameter)
- [Adding additional secrets to existing function](https://github.com/jlichwa/KeyVault-Rotation-RedisKey-PowerShell/tree/master/arm-templates#add-event-subscription-to-existing-functions) - single function can be used for multiple Redis Caches. This template adding new event subscription for secret and necessary permissions to access Redis Cache