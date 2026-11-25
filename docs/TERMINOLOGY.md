# Terminology

Base: A player's Base on a Planet, comprised of multiple Buildings

Building: An individual building on a Base

CONT: A Contract is an agreement between players to buy, sell, or ship commodities

CX: Commodity Exchange at which ask and bid orders for Materials are placed and filled

CXOS: Commodity Exchange Orders (either Ask or Bid)

CXPC: Commodity Exchange Price Chart for a given Material

LM: Local Market. A planet-specific marketplace where Contract listings are posted and discovered (buy, sell, and shipping types).

Material: An inventory item, commodity, building part, production input/output, etc.

Recipe: A production template with material inputs and outputs

Sites: The API uses this term to refer to Bases

Ticker: Usually the material code only (e.g. `RAT`). Some endpoints use `ExchangeTicker` for code+exchange (e.g. `RAT.IC1`). Confirm field names per endpoint shape before filtering.
