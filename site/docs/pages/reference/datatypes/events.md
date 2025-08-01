# Events

Events represent state changes, like a new message or contract event.

Snapchain nodes emit events whenever they observe a state change. Clients can subscribe to a node using the [Events API](../grpcapi/events.md) to get a live stream of changes.

Snapchain keeps events around for 3 days after which they are deleted to save space. To get older data, use the [GRPC](../grpcapi/grpcapi.md) or [HTTP](../httpapi/httpapi.md) APIs.

## HubEvent

| Field        | Type                                                                                                                                                                                                                                                                                                                                              | Label | Description                                                        |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- | ------------------------------------------------------------------ |
| type         | [HubEventType](#HubEventType)                                                                                                                                                                                                                                                                                                                     |       | The type of event                                                  |
| id           | [uint64](#uint64)                                                                                                                                                                                                                                                                                                                                 |       | Unique identifier for the event that encodes block height ordering |
| block_number | [uint64](#uint64)                                                                                                                                                                                                                                                                                                                                 |       | The block number when the event was created                        |
| shard_index  | [uint32](#uint32)                                                                                                                                                                                                                                                                                                                                 |       | The shard index where the event occurred                           |
| timestamp    | [uint64](#uint64)                                                                                                                                                                                                                                                                                                                                 |       | The timestamp when the event was created                           |
| body         | [MergeMessageBody](#mergemessagebody), <br /> [PruneMessageBody](#prunemessagebody), <br /> [RevokeMessageBody](#revokemessagebody), <br />[MergeUserNameProofBody](#mergeusernameproofbody), <br />[MergeOnChainEventBody](#mergeonchaineventbody), <br />[MergeFailureBody](#mergefailurebody), <br />[BlockConfirmedBody](#blockconfirmedbody) | oneOf | The event payload                                                  |

### Event ID Construction

Event IDs are constructed to ensure chronological ordering by block. The ID combines:
- The block height (when the event was created)
- A sequence number within that block

This design allows clients to determine which events occurred in the same block and their relative order, which is useful for processing events in chronological sequence.

## HubEventType

| Name                                | Number | Description |
| ----------------------------------- | ------ | ----------- |
| HUB_EVENT_TYPE_NONE                 | 0      |             |
| HUB_EVENT_TYPE_MERGE_MESSAGE        | 1      |             |
| HUB_EVENT_TYPE_PRUNE_MESSAGE        | 2      |             |
| HUB_EVENT_TYPE_REVOKE_MESSAGE       | 3      |             |
| HUB_EVENT_TYPE_MERGE_USERNAME_PROOF | 6      |             |
| HUB_EVENT_TYPE_MERGE_ON_CHAIN_EVENT | 9      |             |
| HUB_EVENT_TYPE_MERGE_FAILURE        | 10     |             |
| HUB_EVENT_TYPE_BLOCK_CONFIRMED      | 11     |             |

<a name="-MergeMessageBody"></a>

## MergeMessageBody

| Field            | Type                | Label    | Description |
| ---------------- | ------------------- | -------- | ----------- |
| message          | [Message](#Message) |          |             |
| deleted_messages | [Message](#Message) | repeated |             |

<a name="-MergeUserNameProofBody"></a>

## MergeUserNameProofBody

| Field                          | Type                            | Label | Description |
| ------------------------------ | ------------------------------- | ----- | ----------- |
| username_proof                 | [UserNameProof](#UserNameProof) |       |             |
| deleted_username_proof         | [UserNameProof](#UserNameProof) |       |             |
| username_proof_message         | [Message](#Message)             |       |             |
| deleted_username_proof_message | [Message](#Message)             |       |             |

<a name="-PruneMessageBody"></a>

## PruneMessageBody

| Field   | Type                | Label | Description |
| ------- | ------------------- | ----- | ----------- |
| message | [Message](#Message) |       |             |

<a name="-RevokeMessageBody"></a>

## RevokeMessageBody

| Field   | Type                | Label | Description |
| ------- | ------------------- | ----- | ----------- |
| message | [Message](#Message) |       |             |

<a name="-MergeOnChainEventBody"></a>

## MergeOnChainEventBody

| Field          | Type                          | Label | Description |
| -------------- | ----------------------------- | ----- | ----------- |
| on_chain_event | [OnChainEvent](#OnChainEvent) |       |             |

<a name="-HubEventType"></a>

## OnChainEvent

| Field            | Type                                                                                                                                                                                                                                                  | Label | Description                          |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- | ------------------------------------ |
| type             | [OnChainEventType](#OnChainEventType)                                                                                                                                                                                                                 |       | The type of onchain event            |
| chain_id         | [uint32](#)                                                                                                                                                                                                                                           |       | The chain id for the event           |
| block_number     | [uint32](#)                                                                                                                                                                                                                                           |       | The block number for the event       |
| block_hash       | [bytes](#)                                                                                                                                                                                                                                            |       | The block hash for the event         |
| block_timestamp  | [uint64](#)                                                                                                                                                                                                                                           |       | The block timestamp for the event    |
| transaction_hash | [bytes](#)                                                                                                                                                                                                                                            |       | The transaction hash for the event   |
| log_index        | [uint32](#)                                                                                                                                                                                                                                           |       | The log index for the event          |
| fid              | [uint64](#)                                                                                                                                                                                                                                           |       | The fid the event is associated with |
| body             | [SignerEventBody](#signereventbody), <br /> [SignerMigratedEventBody](#signermigratedeventbody), <br /> [IdRegisterEventBody](#idregistereventbody), <br />[StorageRentEventBody](#storagerenteventbody), <br />[TierPurchaseBody](#tierpurchasebody) | oneOf |                                      |
| tx_index         | [uint32](#)                                                                                                                                                                                                                                           |       | The tx index for the event           |

<a name="-OnChainEventType"></a>

## OnChainEventType

| Name                       | Number | Description |
| -------------------------- | ------ | ----------- |
| EVENT_TYPE_NONE            | 0      |             |
| EVENT_TYPE_SIGNER          | 1      |             |
| EVENT_TYPE_SIGNER_MIGRATED | 2      |             |
| EVENT_TYPE_ID_REGISTER     | 3      |             |
| EVENT_TYPE_STORAGE_RENT    | 4      |             |
| EVENT_TYPE_TIER_PURCHASE   | 5      |             |

<a name="-SignerEventBody"></a>

## SignerEventBody

| Field         | Type                                | Label | Description                                        |
| ------------- | ----------------------------------- | ----- | -------------------------------------------------- |
| key           | [bytes](#)                          |       | The bytes of the public key for the signer         |
| key_type      | [uint32](#)                         |       | The type of the key (currently only set to 1)      |
| event_type    | [SignerEventType](#SignerEventType) |       | The type of the signer event                       |
| metadata      | [bytes](#)                          |       | The metadata associated with the key               |
| metadata_type | [uint32](#)                         |       | The type of the metadata (currently only set to 1) |

<a name="-SignerEventType"></a>

## SignerEventType

| Name                          | Number | Description |
| ----------------------------- | ------ | ----------- |
| SIGNER_EVENT_TYPE_NONE        | 0      |             |
| SIGNER_EVENT_TYPE_ADD         | 1      |             |
| SIGNER_EVENT_TYPE_REMOVE      | 2      |             |
| SIGNER_EVENT_TYPE_ADMIN_RESET | 3      |             |

<a name="-SignerMigratedEventBody"></a>

## SignerMigratedEventBody

| Field      | Type        | Label | Description                                              |
| ---------- | ----------- | ----- | -------------------------------------------------------- |
| migratedAt | [uint32](#) |       | The timestamp at which nodes were migrated to OP mainnet |

<a name="-IdRegisterEventBody"></a>

## IdRegisterEventBody

| Field            | Type                                        | Label | Description                                       |
| ---------------- | ------------------------------------------- | ----- | ------------------------------------------------- |
| to               | [bytes](#)                                  |       | The address the fid was registered/transferred to |
| event_type       | [IdRegisterEventType](#IdRegisterEventType) |       | The type of the id register event                 |
| from             | [bytes](#)                                  |       | The address the transfer originated from          |
| recovery_address | [bytes](#)                                  |       | The recovery address for the fid                  |

<a name="-IdRegisterEventType"></a>

## IdRegisterEventType

| Name                                   | Number | Description |
| -------------------------------------- | ------ | ----------- |
| ID_REGISTER_EVENT_TYPE_NONE            | 0      |             |
| ID_REGISTER_EVENT_TYPE_REGISTER        | 1      |             |
| ID_REGISTER_EVENT_TYPE_TRANSFER        | 2      |             |
| ID_REGISTER_EVENT_TYPE_CHANGE_RECOVERY | 3      |             |

<a name="-StorageRentEventBody"></a>

## StorageRentEventBody

| Field  | Type        | Label | Description                                               |
| ------ | ----------- | ----- | --------------------------------------------------------- |
| payer  | [bytes](#)  |       | The address of the payer                                  |
| units  | [uint32](#) |       | The number of units of storage purchased                  |
| expiry | [uint32](#) |       | The timestamp at which these units of storage will expire |

<a name="-TierPurchaseBody"></a>

## TierPurchaseBody

| Field     | Type        | Label | Description                         |
| --------- | ----------- | ----- | ----------------------------------- |
| purchaser | [bytes](#)  |       | The address of the purchaser        |
| tier      | [uint32](#) |       | The tier number purchased           |
| units     | [uint32](#) |       | The number of units purchased       |
| expiry    | [uint32](#) |       | The timestamp at which tier expires |

<a name="-MergeFailureBody"></a>

## MergeFailureBody

| Field   | Type                | Label | Description                      |
| ------- | ------------------- | ----- | -------------------------------- |
| message | [Message](#Message) |       | The message that failed to merge |
| error   | [string](#string)   |       | The reason for the merge failure |

<a name="-BlockConfirmedBody"></a>

## BlockConfirmedBody

| Field        | Type              | Label | Description                              |
| ------------ | ----------------- | ----- | ---------------------------------------- |
| block_number | [uint64](#uint64) |       | The block number that has been confirmed |
| block_hash   | [bytes](#bytes)   |       | The hash of the confirmed block          |
| shard_index  | [uint32](#uint32) |       | The shard index of the confirmed block   |
