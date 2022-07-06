# NEAR Lake Framework PY

Available in programming languages: [Rust](https://github.com/near/near-lake-framework-rs) | [Javascript](https://github.com/near/near-lake-framework-rs) | **Python3**

NEAR Lake Framework is a small library companion to [NEAR Lake](https://github.com/near/near-lake). It allows you to build
your own indexer that subscribes to the stream of blocks from the NEAR Lake data source and create your own logic to process
the NEAR Protocol data.

---

[Official NEAR Lake Framework launch announcement](https://gov.near.org/t/announcement-near-lake-framework-brand-new-word-in-indexer-building-approach/17668) has been published on the NEAR Gov Forum
Greetings from the Data Platform Team! We are happy and proud to announce an MVP release of a brand new word in indexer building approach - NEAR Lake Framework.

---

## Example

```async def main():
    config = near_types.LakeConfig(
        "near-lake-data-mainnet",
        "eu-central-1",
        69130938,
        10
    )

    stream_handle, streamer_messages_queue = streamer(config)
    while True:
        streamer_message = await streamer_messages_queue.get()
        print(f"Received Block #{streamer_message.block.header.height} from Lake Framework")


loop = asyncio.get_event_loop()
cors = asyncio.wait([main()])
loop.run_until_complete(cors)
```

### Tutorials

### More examples

### Dependencies

Install `near-lake-framework`

```bash
$ pip3 install near-lake-framework
```

## Configuration

Everything should be configured before the start of your indexer application via `LakeConfig` struct.

Available parameters:

- `s3_bucket_name: str` - provide the AWS S3 bucket name (`near-lake-testnet`, `near-lake-mainnet` or yours if you run your own NEAR Lake)
- `s3_region_name: str` - provide the region for AWS S3 bucket
- `start_block_height: BlockHeight` - block height to start the stream from
- `blocks_preload_pool_size: int` - provide the number of blocks to preload (default: 200)

## Cost estimates

**TL;DR** approximately $18.15 per month (for AWS S3 access, paid directly to AWS) for the reading of fresh blocks

Explanation:

Assuming NEAR Protocol produces accurately 1 block per second (which is really not, the average block production time is 1.3s). A full day consists of 86400 seconds, that's the max number of blocks that can be produced.

According the [Amazon S3 prices](https://aws.amazon.com/s3/pricing/?nc1=h_ls) `list` requests are charged for $0.005 per 1000 requests and `get` is charged for $0.0004 per 1000 requests.

Calculations (assuming we are following the tip of the network all the time):

```
86400 blocks per day * 5 requests for each block / 1000 requests * $0.0004 per 1k requests = $0.173 * 30 days = $5.19
```
**Note:** 5 requests for each block means we have 4 shards (1 file for common block data and 4 separate files for each shard)

And a number of `list` requests we need to perform for 30 days:

```
86400 blocks per day / 1000 requests * $0.005 per 1k list requests = $0.432 * 30 days = $12.96

$5.19 + $12.96 = $18.15
```

The price depends on the number of shards

## Future plans

We use Milestones with clearly defined acceptance criteria:

* [x] [MVP](https://github.com/near/near-lake-framework/milestone/1)
* [ ] [1.0](https://github.com/near/near-lake-framework/milestone/2)