## toss-up
Fully decentralized betting platform using Chainlink randomness, Ethereum Smartcontracts, Interplanetary FileSystem & React frontend. The betting system is powered by Ethereum Smartcontracts for transparency & immutability. Application assets are stored on the Interplanetary FileSystem (IPFS) for censorship resistance and maximum distribution. The Chainlink Network is used by the TossUp Smartcontract as an oricle to generate randomness and determine ETH and Wei to USD price at the time of transaction.

[![licensebadge](https://img.shields.io/badge/license-CC0_1.0_Universal-blue)](https://github.com/MBrassey/toss-up/blob/main/LICENSE)
[![time tracker](https://wakatime.com/badge/github/MBrassey/toss-up.svg?start=2020-12-26&end=2021-01-02)](https://wakatime.com/@532855a8-3081-4600-a53d-4262beb65d14/projects/mvbemzptww?start=2020-12-26&end=2021-01-02)

#### Issues

- [x] [Initialize React App & Setup](https://github.com/MBrassey/toss-up/issues/1)
- [x] [Deploy Smartcontract to Rinkeby Test Network ](https://github.com/MBrassey/toss-up/issues/2)
- [x] [React/Web3 Components](https://github.com/MBrassey/toss-up/issues/3)
- [x] [Stylize & Test](https://github.com/MBrassey/toss-up/issues/4)
- [x] [Make Readme & Test with Jest](https://github.com/MBrassey/toss-up/issues/5)
- [x] [WalletConnect/Metamask & Deploy to IPFS](https://github.com/MBrassey/toss-up/issues/6)

#### Table of Contents

* [Ethereum](#Ethereum)
* [IPFS](#IPFS)
* [Chainlink](#Chainlink)
* [Requirements](#Requirements)
* [Installation](#Installation)
* [Usage](#Usage)
* [Demo](#Demo)
* [Questions](#Questions)
* [License](#License)

> Application Preview
> [<img src="./src/assets/Screenshot.png">](https://mbrassey-toss-up.on.fleek.co/)

#### Ethereum

The TossUp Smartcontract is deployed live on the Rinkeby Ethereum testnet: [ [0x37465edC8~](https://rinkeby.etherscan.io/address/0x37465edc8d70e4b16033fae23088b1c703924a80#internaltx) ]. The Smartcontract itself is funded with ETH & LINK, which is used to payout winning bets and in exchange for data from Chainlink oracles. The Smartcontract declares the 50% chance in a transparent way, as well as recording each transaction in an immutable decentralized ledger. The betting system is based on a 50% chance equation where a random hash is generated by the Chainlink network to match above or below the halfway mark of possible hashes.

###### Declaring 50% chance, (0.5*(uint256+1))

    uint256 constant half =
        57896044618658097711785492504343953926634992332820282019728792003956564819968;

###### uint256 (uint is an alias) is a unsigned integer which has:

    minimum value of 0
    maximum value of 2^256-1 = 115792089237316195423570985008687907853269984665640564
    039457584007913129639935 //78 decimal digits

###### Send rewards to the winners
    function verdict(uint256 random) public payable onlyVFRC {
        // Check bets from latest betting round, one by one
        for (uint256 i = lastGameId; i < gameId; i++) {
            // Reset winAmount for current user
            uint256 winAmount = 0;

            // If user wins, then receives 2x of their betting amount
            if (
                (random >= half && games[i].bet == 1) ||
                (random < half && games[i].bet == 0)
            ) {
                winAmount = games[i].amount * 2;
                games[i].player.transfer(winAmount);
            }
            emit Result(
                games[i].id,
                games[i].bet,
                games[i].seed,
                games[i].amount,
                games[i].player,
                winAmount,
                random,
                block.timestamp
            );
        }
        // Save current gameId to lastGameId for the next betting round
        lastGameId = gameId;
    }

#### IPFS

TossUp's application files are hosted on the Interplanetary FileSystem [(IPFS)](https://ipfs.io/). Being deployed this way, the application is hosted with peer 2 peer functionality in a completely distributed, decentralized way. TossUp is deployed with and
utilizes the [fleek platform](https://app.fleek.co/) & CDN for the quickest and most reliable IPFS user experience.

#### Chainlink

Chainlink is being used to provide randomness, and thus, fairness to the betting platform. Chainlink nodes are also being used as an oracle or _reliable_ source of information related to the live price of ETH and Wei in USD.

###### Request for randomness

    function getRandomNumber(uint256 userProvidedSeed)
        internal
        returns (bytes32 requestId)
    {
        require(
            LINK.balanceOf(address(this)) > fee,
            "Error, not enough LINK - fill contract with faucet"
        );
        return requestRandomness(keyHash, fee, userProvidedSeed);
    }

###### Callback function used by VRF Coordinator

    function fulfillRandomness(bytes32 requestId, uint256 randomness)
        internal
        override
    {
        randomResult = randomness;

        // Send final random value to the verdict();
        verdict(randomResult);
    }

###### Returns latest ETH/USD price from Chainlink oracles
    function ethInUsd() public view returns (int256) {
        (
            uint80 roundId,
            int256 price,
            uint256 startedAt,
            uint256 timeStamp,
            uint80 answeredInRound
        ) = ethUsd.latestRoundData();

        return price;
    }

    /* ethUsd - latest price from Chainlink oracles (ETH in USD * 10**8).
     * weiUsd - USD in Wei, received by dividing:
     *          ETH in Wei (converted to compatibility with etUsd (10**18 * 10**8)),
     *          by ethUsd.
     */
    function weiInUsd() public view returns (uint256) {
        int256 ethUsd = ethInUsd();
        int256 weiUsd = 10**26 / ethUsd;

        return uint256(weiUsd);
    }

#### Requirements

    node
    npm
    metamask

#### Installation

    npm i

#### Usage

    npm run start
    npm run test a (optional)
    browse: localhost:3001/

<h6><p align="right">:cyclone: Click the image(s) below to view the live <a id="Demo" href="https://mbrassey-toss-up.on.fleek.co/">webapplication</a></p></h6>

> Web3 Modal
> [<img src="./src/assets/Modal.gif">](https://mbrassey-toss-up.on.fleek.co/)

> Bet
> [<img src="./src/assets/Bet.gif">](https://mbrassey-toss-up.on.fleek.co/)

> Setup
> [<img src="./src/assets/Setup.gif">](https://mbrassey-toss-up.on.fleek.co/)

> Tests
> [<img src="./src/assets/Test.gif">](https://wakatime.com/@532855a8-3081-4600-a53d-4262beb65d14/projects/mvbemzptww?start=2020-12-26&end=2021-01-02)

#### Questions

Contact me at [matt@brassey.io](mailto:matt@brassey.io) with any questions or comments.

#### License

`toss-up` is published under the **CC0_1.0_Universal** license.

> The Creative Commons CC0 Public Domain Dedication waives copyright interest in a work you've created and dedicates it to the world-wide public domain. Use CC0 to opt out of copyright entirely and ensure your work has the widest reach. As with the Unlicense and typical software licenses, CC0 disclaims warranties. CC0 is very similar to the Unlicense.

