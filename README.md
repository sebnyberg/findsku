# findsku

CLI for filtering Azure SKUs and regions.

## Motivation

The two commands `az vm list-skus` and `az account locations` are very slow
(roughly 15 seconds to 2 minutes per region for list-skus) and difficult to use
for analysis.

The goal of this tool is to cache the results of the Azure CLI requests, then
allow fast analysis to compare regions and find SKUs matching filtering criteria.

## Security consideration

This tool uses `az vm list-skus` and `az account locations`.

Obviously I trust this tool since I wrote it myself. But you should really not
trust tools like these.

> [!WARNING]
> It is highly recommended that you create a SP with Reader access to the
> Subscription and run the tool in an isolated environment.

## Pre-requisites

- Access to `/tmp`
- Azure CLI (`az` in the path)
- Azure CLI login session (`az login`)

## Installation

For now, only installable with Go:

```bash
go install github.com/sebnyberg/findskus@latest
```

## Usage

> [!IMPORTANT]
> On the first run, locations will be (slowly) downloaded from azure to a
> directory in `/tmp`. To view progress, run in verbose mode with `-v`.

### First run

The first run (and location) will cache results from Azure. View progress with
`-v`.

After changing the subscription, you should re-download data with:
`--redownload`:

```bash
findsku --redownload [...]
```

### Finding SKUs

Find SKUs with 16GB RAM in West Europe:

```bash
findsku --memory 16 --encryption-at-host -l westeurope
```

Find SKUs in any region with at least 64 vCPUs that are available for use:

```bash
findsku --min-cpu 64
```

Find all SKUs with accelerated networking in southcentralus or westus, output as
JSON.

```bash
findsku --accelerated-networking --min-memory 32 -l southcentralus,westus -o json
```

### Listing location statistics

To view location statistics, add `--by-location`:

```bash
findsku --min-memory 16 --max-memory 32 --encryption-at-host --by-location
```

Find locations that has premium IO in at least two availability zones:

```bash
findsku --premium-io --min-zones 2 --by-location
```

