
  ```c++
	CScript GetScriptForGamble(int height, const std::vector<CPubKey>& keys)
	{
	    CScript script;
	    script << OP_GAMBLESCRIPT; // simple version of semantic OP_EVAL
	    script << CScriptNum(height);
	    script << OP_NONCEOF;
	    script << CScript::EncodeOP_N(2);
	    script << OP_MOD;
	    script << CScript::EncodeOP_N(0);
	    script << OP_NUMEQUAL;
	    script << OP_IF;
	    script << ToByteVector(keys[0]);
	    script << OP_ELSE;
	    script << ToByteVector(keys[1]);
	    script << OP_ENDIF;
	    script << OP_CHECKSIG;
	    return script;
	}
  ```
  
  An example on mainnet, to redeem from a gamble script.
  
  ```
	>getnewaddress 'gambler1' legacy 
	GKx8fpnm2RW4ebXq66FhAyym6d6DhLNxiF
	
	>getaddressinfo 
	{
  		"address": "GKx8fpnm2RW4ebXq66FhAyym6d6DhLNxiF",
		...
  		"pubkey": "028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d56",
		...
	}
	
	>getnewaddress 'gambler2' legacy
	GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	
	>getaddressinfo GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	{
  		"address": "GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5",
		...
  		"pubkey": "028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f3",
		...
	}
	
	>getblockcount 
	26428
	
	>creategamble 26500 "[\"028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d56\", \"028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f3\"]"
	{
  		"address": "Sc8FeTEYnLjEL9qVj3Mqz2nWgTEygTV3ds",
  		"redeemScript": "b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac"
	}
	
	>sendtoaddress Sc8FeTEYnLjEL9qVj3Mqz2nWgTEygTV3ds 1 "" "" false
	55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968
	
	>sendtoaddress Sc8FeTEYnLjEL9qVj3Mqz2nWgTEygTV3ds 1 "" "" false
	d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be
	
	>getaddressinfo Sc8FeTEYnLjEL9qVj3Mqz2nWgTEygTV3ds
	{
	  "address": "Sc8FeTEYnLjEL9qVj3Mqz2nWgTEygTV3ds",
	  "scriptPubKey": "a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787",
	  "ismine": false,
	  "iswatchonly": false,
	  "isscript": true,
	  "iswitness": false
	}
	
	# set a redeem address for winner
	>getnewaddress "winner" legacy
	GYMHC1iRhtKsK2GMAEvybkbSeNTtHakScP
	
	# Redeem from "txid=d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be,vout=0"
	# and         "txid=55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968,vout=0"	
	>createrawtransaction "[{\"txid\":\"55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968\", \"vout\":1},{\"txid\":\"d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be\", \"vout\":0}]" "{\"GYMHC1iRhtKsK2GMAEvybkbSeNTtHakScP\":1.99}"
	020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe550100000000ffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d90000000000ffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000

	>dumpprivkey GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993

	>dumpprivkey GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993

 	# Spend utxo locked by redeem script
	# and sign with private key: 
	# KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993
	>signrawtransactionwithkey "020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe550100000000ffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d90000000000ffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000" "[\"KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993\"]" "[{\"txid\":\"55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968\", \"vout\":1, \"scriptPubKey\":\"a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787\", \"redeemScript\":\"b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac\", \"amount\":0.995}, {\"txid\":\"d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be\", \"vout\":0, \"scriptPubKey\":\"a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787\", \"redeemScript\":\"b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac\", \"amount\":0.995}]" "ALL"
	#Note: it will fail as height is not reached.
	#      and, even reached, it may still fail as Gamble condition
	{
  	"hex": "020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe55010000009b473044022079009fc893576c545eca66b6905b8001af2e141c4444931723668ae1e9ecf478022046e2d55e9e6b3343147a5203d279370d9115575eadf85987beff6b4518988b41014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d9000000009c483045022100bcfc86cc8cf001e2d9bd26f2a392cd2d5f14bac0ba0e9c653cda1709c8b85423022019446e21beb9728361fc46803646b162d34d10565d214723ffe53b8d13f25793014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000",
  	"complete": false,
  	"errors": [{
      		"txid": "55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968",
      		"vout": 1,
      		"witness": [],
      		"scriptSig": "473044022079009fc893576c545eca66b6905b8001af2e141c4444931723668ae1e9ecf478022046e2d55e9e6b3343147a5203d279370d9115575eadf85987beff6b4518988b41014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac",
      		"sequence": 4294967295,
      		"error": "Unable to sign input, invalid stack size (possibly missing key)"
    	},
    	{
      		"txid": "d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be",
      		"vout": 0,
      		"witness": [],
      		"scriptSig": "483045022100bcfc86cc8cf001e2d9bd26f2a392cd2d5f14bac0ba0e9c653cda1709c8b85423022019446e21beb9728361fc46803646b162d34d10565d214723ffe53b8d13f25793014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac",
      		"sequence": 4294967295,
      		"error": "Unable to sign input, invalid stack size (possibly missing key)"
    	}]
	}
	
	# after a rest of weekend, blockchain reached 26500, 
	# let's sign it again.
	>signrawtransactionwithkey "020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe550100000000ffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d90000000000ffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000" "[\"KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993\"]" "[{\"txid\":\"55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968\", \"vout\":1, \"scriptPubKey\":\"a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787\", \"redeemScript\":\"b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac\", \"amount\":0.995}, {\"txid\":\"d9e4f6f4c82b89df9ecefba343b16cacfa844f2d9c7e4496f22dd5df6a7943be\", \"vout\":0, \"scriptPubKey\":\"a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787\", \"redeemScript\":\"b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac\", \"amount\":0.995}]" "ALL"
	{
  		"hex": "020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe55010000009b473044022079009fc893576c545eca66b6905b8001af2e141c4444931723668ae1e9ecf478022046e2d55e9e6b3343147a5203d279370d9115575eadf85987beff6b4518988b41014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d9000000009c483045022100bcfc86cc8cf001e2d9bd26f2a392cd2d5f14bac0ba0e9c653cda1709c8b85423022019446e21beb9728361fc46803646b162d34d10565d214723ffe53b8d13f25793014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000",
  		"complete": true
	}

	# Now redeem it on the network.
	>sendrawtransaction 020000000268a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe55010000009b473044022079009fc893576c545eca66b6905b8001af2e141c4444931723668ae1e9ecf478022046e2d55e9e6b3343147a5203d279370d9115575eadf85987beff6b4518988b41014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffffbe43796adfd52df296447e9c2d4f84faac6cb143a3fbce9edf892bc8f4f6e4d9000000009c483045022100bcfc86cc8cf001e2d9bd26f2a392cd2d5f14bac0ba0e9c653cda1709c8b85423022019446e21beb9728361fc46803646b162d34d10565d214723ffe53b8d13f25793014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffff01c07fdc0b000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000
	
	# You can check my all above operations result on this tx by txid:
	# https://bitgold.bitbaba.com/tx/8e480645861b3d28d46989dfef53b46b4dc50cf192ed20605a1dae20560c99a9

  ```
  
  Redeem on each utxo locked by gamble script
  
  ```
	# You can also redeem on sigle utxo
	>createrawtransaction "[{\"txid\":\"55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968\", \"vout\":1}]" "{\"GYMHC1iRhtKsK2GMAEvybkbSeNTtHakScP\":0.995}"
	020000000168a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe550100000000ffffffff01e03fee05000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000

	>dumpprivkey GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993

	>dumpprivkey GQSyTMjRoUsCaVfuU4KzfgVha3t642V1X5
	KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993

	# Sign with single utxo
	>signrawtransactionwithkey "020000000168a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe550100000000ffffffff01e03fee05000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000" "[\"KxgwfzDHHRFCqKWsWNCgtjAFbaHKWWZsmS6bdJzGt5W98kVDR993\"]"  "[{\"txid\":\"55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968\", 	\"vout\":1, 	\"scriptPubKey\":\"a914a2b1ed20c88a9f197b7009053a6ecba9807d8ed787\", 	\"redeemScript\":\"b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac\", \"amount\":0.995}]"  "ALL"
	#Note: it will fail as height is not reached.
	#      and, even reached, it may still fail as Gamble condition
 	{
  	"hex": "020000000168a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe55010000009b47304402202da51a5f505f12e2b1d8917a997c915833caace877b9268e637ea5639352c831022060dbd8cdf7b91c6016ebb3aecbcbb13374f6e86b4d0c630f208bc72528b09ea5014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffff01e03fee05000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000",
  	"complete": false,
  	"errors": [{
	  	"txid": "55fe01a00ea0ab336b77c0ef8519fe7865f912f128e8e5db6225185dab6fa968",
	  	"vout": 1,
	  	"witness": [],
	  	"scriptSig": "47304402202da51a5f505f12e2b1d8917a997c915833caace877b9268e637ea5639352c831022060dbd8cdf7b91c6016ebb3aecbcbb13374f6e86b4d0c630f208bc72528b09ea5014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368ac",
	  	"sequence": 4294967295,
	  	"error": "Unable to sign input, invalid stack size (possibly missing key)"
	}]
	}
	
	# after blockchain reached 26500, 
	# sign will succeed. 
	>signrawtransactionwithkey(…)
	{
	  "hex": "020000000168a96fab5d182562dbe5e828f112f96578fe1985efc0776b33aba00ea001fe55010000009b47304402202da51a5f505f12e2b1d8917a997c915833caace877b9268e637ea5639352c831022060dbd8cdf7b91c6016ebb3aecbcbb13374f6e86b4d0c630f208bc72528b09ea5014c51b9028467b75297009c6321028ae98df9f28ead984f980393e7bfd865d80a5b484300755dbf25870e4f8f1d566721028b040ecb3c4d87e91a82e3f3dd3196b53de8fdb840577e7f8556b48d3ac6d7f368acffffffff01e03fee05000000001976a9149f1f2a4013414b22dfb9aafd0e354fd83e45ec1888ac00000000",
	  "complete": true
	}
	
	# here end with no broadcast test,
	# as we have broacasted spending of the two UTXOs on network.
	# error: missing inputs!
  ```
  
