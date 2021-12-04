# CardanoAccessToken

I'm posting this concept here as well for further work and development on it, as well as recording tests and proof of concepts.

Original Post Link: https://www.reddit.com/r/cardano/comments/r8c1xm/a_possible_solution_to_the_issue_i_raised_with/


Original Post Content:
------------------TL;DR

So this is the issue of trustless signer validation, you might call it, wherein Alice has locked some funds in a smartcontract and now she wants to unlock them (and presumably in this instance she should be the only one capable of doing this). And say you have a smartcontract which validates this request by checking that the signer of the unlocking TX matches a presaved/hashed value in the datum.

The problem comes to trust..trust that the datum was entered as you expect. When it comes to matching the pubkeyhash, wherein "only the owner of this pubkeyhash can unlock", if the datum is compromised the attacker can put their own pubkeyhash and unlock your funds.

So the solution is for users to mint a coin which is named their pubkeyhash. This is an access token and works like this:

Alice locks funds at a smartcontract and includs her "general access" token, proving publicly she locked it, and only possible to have deposited from her wallet.

Alice goes to unlock and sends her general access token with her unlock tx. The smartcontract then validates her only if she as the signer -> the token she sent -> and the token at the smartcontract all match up. It then allows the unlock and only allows her access tokens to be sent back to her.

addtional thoughts: You could even utilize these access tokens to store other access related data, such as other pubkeyhashes, etc for other utility use. And some smartcontracts might be written to include only validation that the token names match, but not the signer, in case of a simple dynamic locking token for storing funds for someone else, sending them the access token, they unlock using it and in the smartcontract it enforces that those tokens now return back to you as their owner.

edit: I think we need to spec out how the name would be structured in case of using additional data stored there.

Edit: Also there's no threat to someone "stealing" somehow your access tokens. Because to access things, the access token name is the pubkeyhash of you, so they must match or it fails. So you could send your access token to someone else and it's useless to them. And by the same token (pun intended) it's useless to mint a token matching someone else's pubkeyhash.

----------------------

If my understanding is correct (I'm an amateur at Haskell/Plutus) then what I have may be a solution to the issue I raised with MuesliSwap, specifically how validation relies on trusting the datum has been entered accurately, and how to make a smartcontract be the primary if not sole reliance on that validation.

The smartcontract can extract the name of a native token in its input via the script context. It can also see who signed this transaction. So if the owner of an address had minted a native token with the name == their pubkeyhash, then a smartcontract could enforce that an input must contain that matching token being spent back to itself only as well as a script input containing that token which would have been placed there during locking...and it could also check the datum contains the matching keyhash.

From my own experience working with Haskell and building smartcontracts I believe this would work? Someone could even create a minting smartcontract for general use which just mints tokens with names matching your keyhash, in large batches.

In addition, if the name length limit is 64 you would have some room for additional data for special use cases. And in addition, such a token should have meta matching, with any other interesting data one might want. Then essentially you have this self-minted, key matching "general use access" token which is used in unlocking pertinent transactions.

This would be a dapp-wide solution to this issue of proving you are indeed the rightful owner of said unlocking transaction, a problem which it seems is effecting more then just MuesliSwap.

So for validation in a smartcontract you would want 2 conditions:

TX In contains nft with name == signer of this transaction, 1 from the script and 1 from the signer, and both tokens full names must match.

The tx-ins containing the nft are being spent to address == pubkeyhash/signer/nftname

With that simple validation, any smartcontract where you only want the original locker to unlock, this can be easily enforced without regard as to the datum risks in using webapps, etc. The entirety of the procedure of validation is builtin and totally onchain. A person would probably want a good amount as you would always lock 1 at these such smartcontracts.

I'm eager to hear thoughts and if I got it right conceptually. I'll be doing some testing with it and seeing if I can build it to prove it in practice.
