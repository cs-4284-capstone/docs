# Welcome to Soundbin
Demo Video: https://youtu.be/S8PYpyXdU1c

Soundbin is our entry for the 2019 VT Blockchain challenge. Soundbin is a decentralized music platform that allows artists to independently distribute their content directly to their fans without any middlemen. The site was inspired by the problems faced by artists using current online distribution services such as Spotify or Soundcloud, which either pay fractions of a cent per play or take a large cut of all total sales. In addition, these services also host music centrally, which means that if anything happens to them (they go down, change their terms, etc) the content uploaded goes along with it. Using the distributed EOS.IO network as a payments backend, we have built a service with a low barrier of entry so that VT students can easily set up and start uploading and selling their original content on their own hardware with only a small amount of command-line knowledge. Additionally, customers using Soundbin sites can take comfort in knowing that all of the money they spend on music will go directly into the pockets of the artist who created it.

Because of our team members’ varying skillsets, we decided to build Soundbin using a microservice-oriented architecture, with each member ‘owning’ a set of components that most closely matched their skillset. By wrapping these services in docker containers, we created an easily-deployable stack that can be launched with only a few Docker Compose commands. The customer uses the frontend webapp (written in Vue) to browse the artist’s catalog, and enters their email and EOS wallet id to create a purchase. Customers can buy either individual songs or entire albums using this interface. They then send tokens along with a purchase memo (generated by the webapp) to the artist’s Smart Contract, which validates that the amount sent matches the price of the order, and writes the purchase to the blockchain. To dispatch purchases when the money comes in, a separate node continuously reads the blockchain and writes new transactions to a Mongo database. These transactions are monitored by a piece of software called Demux, which checks for any song purchase actions; upon finding which it calls our backend server, which uploads the requested tracks to Firefox Send (a limited-time file-sharing service) and sends the customer an email containing a one-time link where they can download their purchased content. Creators log into a separate admin panel, where they can create new albums and upload new tracks. Here, they can also see the purchases made on their site and track their customer list.

## Codebase Organization
Because of our microservice-based approach, we have divided our code into several different repositories in order to seperate concerns. You can find the key services here:
* [soundbin-launcher](https://github.com/cs-4284-capstone/soundbin-launcher) - Contains code to start everything up using docker-compose. (Go here next, if you're just starting up)
* [artist-frontend](https://github.com/cs-4284-capstone/artist-frontend) - A Vue webapp that customers use to browse the catalog and start making purchases.
* [blockchain-backend](https://github.com/cs-4284-capstone/blockchain_backend) - Contains all EOS-facing code, including smart contracts and docker code to run our local nodeos instance.
* [demux](https://github.com/cs-4284-capstone/demux) - ExpressJS Blockchain notifiaction reader. Monitors the blockchain for the reciept of any tokens, then informs the admin server to deliver the purchased content.
* [soundbin-admin](https://github.com/cs-4284-capstone/soundbin-admin) - Core administration server, written in Django. Handles music uploads, purchases, and deliveries.

## Things you need to run Soundbin
You need the following items at-hand in order to run our code:
* A host capable of running docker and docker-compose, with `curl` installed.
* An EOS.IO account with a private key.
* A GMail account to send songs from
