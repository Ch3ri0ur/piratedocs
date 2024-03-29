# Overview Pirate Map

The Map is the umbrella term for the signaling and finding of a secure route to the project. 

[Getting Started](10-map-getting-started.md){: .md-button .md-button--primary }

## Requirements

* A reverse proxy to unify the endpoints of the pirate module.
    * ensuring secure connection via HTTPS
        * This necessitates a domain, DNS and certificate.
* A method to point users to the possible projects and route the queries above the reverse proxies of the individual moduls.

[Requirements](20-map-requirements.md){: .md-button  }

## Implementation

For the reverse proxy, needed to unify the endpoints, a server implementation called [[caddy.md]] is used. It takes all requests to the Bridge, Flag and the Spyglass and directs them to the correct components. It also provides a secure [[HTTP]]S connection for all clients.

[Further details on Implementation](30-map-implementation.md){: .md-button}

## Validation and Future Steps

In this section the requirements are compared with the current state of the project and future steps outlined.

[Validation](40-map-validation.md){: .md-button}

