# Some Questions to Explore

Following are some questions to the necessity or other aspects of this solution which I'm going to try to address as best I can from my understand.

## Why Use 2 CATs? Why not just 1 CAT locked which you must unlock and can prove that your unlock-signing wallet matches the datum value + catname?
If using whole pubkey in cat name: See https://github.com/MadeWithLovelace/CATs-CardanoAccessTokens/issues/3

If using partial pubkey in catname: Although it seem unlikely (I don't know the probability) I would rather assume the improbable is possible and protect against it, especially if it is of little consequence or very easy to achieve. The unlikely/improbable situation is: A malicious actor has a pubkeyhash where the last 28 bytes match with yours and that malicious actor is in control of a webapp you are using to lock funs. Because they control the datum entry, and forming the structure of the transaction before it's sent to be built, it's possible they could then detect this condition of matching final 28bytes and insert their leading 28 bytes from their pubkey into the datum AND include one of their CATs masquerading as yours. While you should detect this at TX signing in your wallet, say you don't. They could then unlock the funds later.

By adding a comparison of the CAT Policy ID at the locked TX with the CAT you are sending in the unlock TX, this would be an impossible situation.

## What if someone manages to steal or get ahold of your CATs?
Because the way this is implemented requires that the datum stored leading 28bytes of your pubkey + the catname MUST equal your signing wallet's pubkey, this would be a fruitless endeavor as they could not unlock your funds without your signing key.

However: There are scenarios where you would want to allow a non-CAT-owner to use another owner's CAT for unlocking, particularly if locking funds for someone and then "lending" them a CAT at some date/point to use to unlock those funds. The smartcontract in that case would allow this and would still enforce sending the CAT back to the owner, so it's back to you.
