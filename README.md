# poagov

A tool to monitor the POA Network's blockchain for
[governance events](https://github.com/poanetwork/wiki/wiki/Governance-Overview).

# Building

To build the `poagov` CLI tool, run the following:

    $ git clone https://gitlab.com/DrPeterVanNostrand/poagov.git
    $ cd poagov
    $ cargo build --release

# Usage

Once you have built `poagov`, you can print out the CLI usage by running:

    $ ./target/release/poagov --help

    poagov 1.0
    Monitores the POA Network's blockchain for governance events.

    USAGE:
        poagov [FLAGS] [OPTIONS]

    FLAGS:
            --core        monitor voting contracts deployed to the Core network (same as using --network=core)
            --earliest    start monitoring for goverance events starting from the first block in the chain
            --email       send governance notifications via email
        -h, --help        prints help information
        -k                monitor the blockchain for ballots to change keys (same as --monitor=keys)
            --latest      start monitoring for goverance events starting from the most recently mined block in the chain
            --local       monitor voting contracts deployed to a locally running POA chain (same as using --network=local)
        -p                monitor the change for ballots to change the proxy address (same as --monitor=proxy)
            --push        send governance notifications via push notification
            --sokol       monitor voting contracts deployed to the Sokol test network (same as using --network=sokol)
        -t                monitor the chain for ballots to change the minimum threshold (same as --monitor=threshold)
        -V, --version     prints version information

    OPTIONS:
        --block-time <value>    the average time it takes to mine a new block
        --monitor <value>       a comma-separated list of ballot types to monitor for governance events; the available values are: keys, threshold, proxy
        --network <value>       the name of the network to monitor for ballots; the values available for this option are: core, sokol, local
        --rpc <value>           the URL for the RPC endpoint
        --start <value>         start monitoring for governance events at this block (inclusive)
        --tail <value>          start monitoring for governance events for the `n` blocks prior to the last mined block in the chain

# Setting up Email Notifications

In order to enable email notifications, you must change the name of the
`sample.env` file to `.env`. Then, you must add values for the following
SMTP config options in your `.env` file:

    SMTP_HOST_DOMAIN=
    SMTP_USERNAME=
    SMTP_PASSWORD=
    OUTGOING_EMAIL_ADDRESS=

Add a comma-separated list of email address to the "VALIDATORS" config
option in your .env file. These addresses will be sent emails when `poagov`
encounters governance events on the POA blockchain.

# An Explained Example

    $ ./target/release/poagov --sokol --earliest -kt --email

- `--sokol` is used to monitor contracts deployed to POA's test network.
- `--earliest` starts monitoring from the first block in the blockchain.
- `-k` get notifications for ballots to change keys.
- `-t` get notifications for ballots to change the min threshold.
- `--email` sends out email notifications to each address in the
"VALIDATORS" config value (located in the .env file).

Press [ctrl-c] to exit `poagov`.

# Logs

Logs are output to stderr. Any notifications that were generated, sent,
or failed to be sent will be logged. The following is an example log for a
a notification for a ballot to change the min threshold that was generated
using the command `$ poagov --earliest -t`:

    Apr 21 08:31:54.219 INFO notification, data: Threshold(
        ThresholdNotification {
            network: Sokol,
            endpoint: "https://sokol.poa.network",
            block_number: 1078816,
            contract_type: Threshold,
            ballot_type: ChangeMinThreshold,
            ballot_id: 2,
            start_time: 2018-02-23T05:28:22Z,
            end_time: 2018-02-25T05:33:00Z,
            memo: "*TEST* ballot to increase the consensus threshold to 51% (rounded to the higher integer) of the total number of validators. The idea is to legitimize passing the ballot by the majority participation.",
            proposed_value: 4
        }
    )
