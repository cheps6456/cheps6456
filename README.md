use crate::non_fungible_token::token::*;
use gstd::{exec, msg, prelude::*, ActorId};

#[derive(Debug, Encode, Decode, TypeInfo, Clone)]
pub struct DelegatedApproveMessage {
    pub token_owner_id: ActorId,
    pub approved_actor_id: ActorId,
    pub nft_program_id: ActorId,
    pub token_id: TokenId,
    pub expiration_timestamp: u64,
}

impl DelegatedApproveMessage {
    pub(crate) fn validate(&self, signed_approve: &[u8], true_token_owner: &ActorId) {
        if msg::source() != self.approved_actor_id {
            panic!("Source is wrong, msg::source must be equal to approved_actor_id")
        }

        if exec::program_id() != self.nft_program_id {
            panic!("You have tried to use delegated_approve with wrong program")
        }

        if self.approved_actor_id == ActorId::zero() {
            panic!("Zero address, just use burn if you want to remove token");
        }

        if true_token_owner != &self.token_owner_id {
            panic!("This user doesn't own the token")
        }

        if exec::block_timestamp() >= self.expiration_timestamp {
            panic!("Delegated approve has expired")
        }

        let owner: [u8; 32] = self.token_owner_id.into();
        if sr25519::verify(signed_approve, self.encode(), owner).is_err() {
            panic!("Failed sign verification");
        }
    }
}


npm install --global yarn 
git clone https://github.com/gear-dapps/react-app.git nombredelproyecto
REACT_APP_NODE_ADDRESS=wss://testnet.vara.rs
import { GearApi } from "@gear-js/api";
import { Button } from "@gear-js/ui";
import { useState } from "react";

function NodeData() {
  const [chainData, setChain] = useState<string>();
  const [nodeNameData, setNodeName] = useState<string>();
  const [nodeVersionData, setNodeVersion] = useState<string>();

  const nodeInformation = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const [chain, nodeName, nodeVersion] = await Promise.all([
      gearApi.chain(),
      gearApi.nodeName(),
      gearApi.nodeVersion(),
    ]);

    setChain(chain);
    setNodeName(nodeName);
    setNodeVersion(nodeVersion);
  };

  return (
    <div className="card">
      <h3>Node Data</h3>
      <p>Red: {chainData}</p>
      <p>Nodo: {nodeNameData}</p>
      <p>Version del nodo:{nodeVersionData}</p>
      <Button text="Get Node Information" onClick={nodeInformation} />
    </div>
  );
}

export { NodeData }; import { GearApi } from '@gear-js/api';
import { useEffect, useState } from 'react';

function Blocknumber () {

  const [blocknumber, setBlocknumber] = useState();

  const blocknumbers =  async function connect() {
        const gearApi = await GearApi.create({
            providerAddress: 'wss://rpc-node.gear-tech.io',
          });
             
        await gearApi.gearEvents.subscribeToNewBlocks((header:any) => {
         
          setBlocknumber(header.number.toNumber());

        });

        
      }

      useEffect(()=>{   
        blocknumbers();
      })
return (
   <div className="card">
    <h3>Block Number</h3>
    <p>{blocknumber}</p>
  </div>
  
)
}

export {Blocknumber};

import { useState } from 'react';
import { GearKeyring } from '@gear-js/api';
import { Button } from '@gear-js/ui';

function Mnemonic () {

const [mnemonictext, setMnemonictext]=useState("");
  
const generatemnemonic = () => {

    const mnemonic = GearKeyring.generateMnemonic();
    setMnemonictext(mnemonic);

 };

return (

  <div className="card">
    <h3>Generate Mnemonic</h3>
    <p className="mnemonic">{mnemonictext}</p>
    <Button text="Get Mnemonic" onClick={generatemnemonic} />
  </div>
)
}

export {Mnemonic};

import { GearApi } from "@gear-js/api";
import { Button } from "@gear-js/ui";
import { useState } from "react";

function GasData() {
  const [minlimit, setMinlimit] = useState<any | undefined>(0);
  const [reserved, setReserved] = useState<any | undefined>(0);
  const [burned, setBurned] = useState<any | undefined>(0);
  const [maybereturned, setMaybereturned] = useState<any | undefined>(0);
  const [waited, setWaited] = useState<any | undefined>();

  const getGasData = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const codeId =
      "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";

    const gas = await gearApi.program.calculateGas.initCreate(
      "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d",
      codeId,
      "0x00",
      0,
      true
    );

    setMinlimit(gas.toHuman().min_limit);
    setMaybereturned(gas.toHuman().maybereturned);
    setBurned(gas.toHuman().burned);
    setReserved(gas.toHuman().reserved);
    setWaited(gas.toHuman().waited);
  };

  return (
    <div className="container">
      <h1>Gas fee</h1>
      <p> Minlimit: {minlimit}</p>
      <p> Reserved: {reserved}</p>
      <p> Burned: {burned}</p>
      <p> May be returned: {maybereturned}</p>
      <p> Waited: {waited}</p>
      <Button text="Get Gas Data" onClick={getGasData} />
    </div>
  );
}

export { GasData };

import { GearApi } from "@gear-js/api";
import { Button } from "@gear-js/ui";
import { useState } from "react";

function GasLimit() {
  const [minlimit, setMinlimit] = useState<any | undefined>(0);

  const calculategas = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const codeId = "Dirección del código de tu contrato";

    const gas = await gearApi.program.calculateGas.initCreate(
      "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d",
      codeId,
      "0x00",
      0,
      true
    );

    setMinlimit(gas.toHuman().min_limit);

    return gas.toHuman().min_limit;
  };

  return (
    <div className="container">
      <h1>Gas fee</h1>
      <p className="mnemonic"> minlimit: {minlimit}</p>
      <Button text="Calculate gas" onClick={calculategas} />
    </div>
  );
}

export { GasLimit};
import { useAccount } from "@gear-js/react-hooks";

function BalanceAccount() {
  const { account } = useAccount();

  return (
    <>
      <h2>
        sender : {account?.address} Balance: {account?.balance.value}
      </h2>
    </>
  );
}
export { BalanceAccount};
import { useAccount } from "@gear-js/react-hooks";

function BalancesAllAccounts() {
  const { accounts } = useAccount();

  return (
    <div>
      {accounts.map((account) => (
        <div key={account.address}>
          <button type="button">{account.meta.name}</button> {account.address}
        </div>
      ))}
    </div>
  );
}
export { BalancesAllAccounts };
import { useState } from "react";
import { useAccount } from "@gear-js/react-hooks";
import { Button, Modal } from "@gear-js/ui";

function AccountsExtensionModal() {
  const { accounts } = useAccount();
  const [isModalOpen, setIsModalOpen] = useState(false);

  const openModal = () => {
    setIsModalOpen(true);
  };

  const closeModal = () => {
    setIsModalOpen(false);
  };

  return (
    <>
      <Button text="Get accounts" onClick={openModal} />
      {isModalOpen && (
        <Modal size="large" heading="Connect" close={closeModal}>
          <div>
            {accounts.map((account) => (
              <div key={account.address}>
                <button type="button">{account.meta.name}</button>{" "}
                {account.address}
              </div>
            ))}
          </div>
        </Modal>
      )}
    </>
  );
}

export { AccountsExtensionModal };

import { GearApi, decodeAddress } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useAlert } from "@gear-js/react-hooks";
import { useState } from "react";
import { isAddress } from "@polkadot/util-crypto";

function Mailbox() {
  const alert = useAlert();
  const [list, setList] = useState<any | undefined>([]);
  const [address, setAddress] = useState<any | undefined>("");

  const getmailbox = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    // Get mailbox
    if (isAddress(address)) {
      const mailbox = await gearApi.mailbox.read(decodeAddress(address));
      console.log(address);
      setList(mailbox);
      console.log(mailbox);
    } else {
      alert.error("Add valid public address");
    }
  };

  const AddressInputChange = (event: any) => {
    setAddress(event.target.value);
  };

  return (
    <div className="mailbox">
      <center>Mailbox</center>
      <center className="container">
        Introduce your Address
        <Input type="text" value={address} onChange={AddressInputChange} />
        <Button text="Get Mailbox" onClick={getmailbox} />
      </center>
    </div>
  );
}
export { Mailbox };

import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function Waitlist() {
  const [list, setList] = useState<any | undefined>([]);
  const [codeID, setCodeID] = useState<any | undefined>("");

  const getwaitlist = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const waitlist = await gearApi.waitlist.read(codeID);
    setList(waitlist);
    console.log(waitlist);
  };

  const CodeIDInputChange = (event: any) => {
    setCodeID(event.target.value);
  };

  return (
    <div className="waitlist">
      <center>Waitlist</center>
      <center className="container">
        Introduce your codeID
        <Input type="text" value={codeID} onChange={CodeIDInputChange} />
        <Button text="Get Waitlist" onClick={getwaitlist} />
      </center>
    </div>
  );
}

export { Waitlist }; import { getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";
import { useState } from "react";
import { useApi, useAlert } from "@gear-js/react-hooks";
import { AnyJson } from "@polkadot/types/types";

function ReadState() {
  const { api } = useApi();

  const alert = useAlert();

  const [fullState, setFullState] = useState<AnyJson>();

  const codeId =
    "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";

  const programId =
    "0x5ecf51f6f6d58093dbcd1fb450b7948fc5d07f947f975e07d52fa4d8f9c5e94b";

  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const getState = () => {
    const metadata = getProgramMetadata("0x" + meta);

    api.programState
      .read({ programId: programId }, metadata)
      .then((result) => {
        setFullState(result.toJSON());
        alert.success("Successful state");
      })
      .catch(({ message }: Error) => alert.error(message));
  };

  return (
    <div className="container">
      <center>Full State</center>
      <center className="state">
        State
        <p className="text"> {JSON.stringify(fullState)}</p>
      </center>
      <Button text="Get Full State" onClick={getState} />
    </div>
  );
}

export { ReadState };
import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetTimestamp() {
  const [blockhash, setBlockhash] = useState<any | undefined>(0);
  const [timestamp, setTimestamp] = useState<any | undefined>(0);
  const [year, setYear] = useState<any | undefined>(0);
  const [month, setMonth] = useState<any | undefined>(0);
  const [minutes, setMinutes] = useState<any | undefined>(0);
  const [seconds, setSeconds] = useState<any | undefined>(0);
  const [hour, setHour] = useState<any | undefined>(0);
  const [day, setDay] = useState<any | undefined>(0);
 

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getTimestamp = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const ts = await gearApi.blocks.getBlockTimestamp(blockhash);
    setTimestamp(ts.toNumber());

    const fecha = new Date(ts.toNumber());
    setYear(fecha.getFullYear());
    setMonth(fecha.getMonth() + 1);
    setDay(fecha.getDate());
    setHour(fecha.getHours());
    setMinutes(fecha.getMinutes());
    setSeconds(fecha.getSeconds());
    
  };

  const AmountInputChange = async (event: any) => {
    setBlockhash(event.target.value);
  };

  return (
    <>
    <div className="card">
      <center>Get Timestamp</center>
      <Input type="text" value={blockhash} onChange={AmountInputChange} />
      <div className="horizontal">
        <Button text="Subscribe blocks" onClick={subscribeblocks} />
        <Button text="Get Timestamp" onClick={getTimestamp} />
      </div>
    </div>
    <div className="card">
    <center>Output</center>
    <center>Timestamp: {timestamp}</center>
    <center>Year: {year}</center>
    <center>Month: {month}</center>
    <center>Day: {day}</center>
    <center>Hour: {hour}</center>
    <center>Minutes: {minutes}</center>
    <center>Seconds: {seconds}</center>
    </div>
    </>
  );
}
import { GearApi, GearKeyring, getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";
import { useState } from "react";

function SendMessage() {
  const [status, setStatus] = useState<any | undefined>("");

  const sendmessage = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const codeId =
      "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";
    const programId =
      "0x4d47b38aa8a9b1f896283445328a43802d95f2fb413017aa90d1f668809b24d1";
    const somePayload = "0x676574";
    const meta = getProgramMetadata(codeId);
    const keyring = await GearKeyring.fromSuri("//Alice");

    const gas = await gearApi.program.calculateGas.initCreate(
      "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d",
      codeId,
      "0x00",
      0,
      true
    );

    try {
      const message: any = {
        destination: programId, // programId
        payload: somePayload,
        gasLimit: 40000000,
        value: 1000,
      };

      const extrinsic: any = gearApi.message.send(message, meta);
      await extrinsic.signAndSend(keyring, (event: any) => {
        console.log(event.toHuman());
      });
    } catch (error: any) {
      console.error(`${error.name}: ${error.message}`);
    }
  };

  return (
    <div className="container">
      <h1>Send Message</h1>
      <p className="mnemonic">Status: {status} </p>
      <Button text="Send Message" onClick={sendmessage} />
    </div>
  );
}

export { SendMessage };
import { Button } from "@gear-js/ui";
import { GearApi, getProgramMetadata, GearKeyring } from "@gear-js/api";
import { useAlert } from "@gear-js/react-hooks";

function UserMessageSent() {
  const alert = useAlert();

  const Usermessagesent = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const codeId =
      "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";
    const programId =
      "0x4d47b38aa8a9b1f896283445328a43802d95f2fb413017aa90d1f668809b24d1";

    const somePayload = "0x676574"; // get "0x676574" inc: '0x696E63'
    const meta: any = getProgramMetadata(programId);
    const keyring = await GearKeyring.fromSuri("//Alice");

    const message: any = {
      destination: programId, // programId
      payload: somePayload,
      gasLimit: 899819245,
      value: 1000,
    };

    const extrinsic: any = gearApi.message.send(message, meta);
    await extrinsic.signAndSend(keyring, (event: any) => {
      console.log(event.toHuman());
      alert.success(Object.keys(event.toHuman().status));
    });

    // Usermessagesent subscribtion
    const unsub = gearApi.gearEvents.subscribeToGearEvent(
      "UserMessageSent",
      ({
        data: {
          message: { id, source, destination, payload, value },
        },
      }) => {
        console.log(`
        messageId: ${id.toHex()}
        source: ${source.toHex()}
        payload: ${payload.toHuman()}
        `);
      }
    );
  };

  return (
    <div className="container">
      <h1>User Message Sent Event</h1>
      <p className="mnemonic">Subscription: </p>
      <Button text="Usermessagesent" onClick={Usermessagesent} />
    </div>
  );
}

export { UserMessageSent }; import { Button } from "@gear-js/ui";
import { GearApi, getProgramMetadata, GearKeyring } from "@gear-js/api";
import { useAlert } from "@gear-js/react-hooks";

function MessageQueued() {
  const alert = useAlert();

  const Messagequeued = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const codeId =
      "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";
    const programId =
      "0x4d47b38aa8a9b1f896283445328a43802d95f2fb413017aa90d1f668809b24d1";

    const somePayload = "0x676574"; // get "0x676574" inc: '0x696E63'
    const meta: any = getProgramMetadata(programId);
    const keyring = await GearKeyring.fromSuri("//Alice");

    const message: any = {
      destination: programId, // programId
      payload: somePayload,
      gasLimit: 899819245,
      value: 1000,
    };

    const extrinsic: any = gearApi.message.send(message, meta);
    await extrinsic.signAndSend(keyring, (event: any) => {
      console.log(event.toHuman());
      alert.success(Object.keys(event.toHuman().status));
    });

    // MessageQueued subscribtion
    const unsub = gearApi.gearEvents.subscribeToGearEvent(
      "MessageQueued",
      ({ data: { id, source, destination, entry } }) => {
        console.log({
          messageId: id.toHex(),
          programId: destination.toHex(),
          userId: source.toHex(),
          entry: entry.isInit
            ? entry.asInit
            : entry.isHandle
            ? entry.asHandle
            : entry.asReply,
        });
      }
    );
  };

  return (
    <div className="container">
      <h1>Message Queued Event</h1>
      <p className="mnemonic">Subscriptions: </p>
      <Button text="MessageQueued" onClick={Messagequeued} />
    </div>
  );
}

export { MessageQueued };
import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetDataWithBlocknumber() {
  const [valueAmount, setValueAmount] = useState<any | undefined>(0);
 
  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getDataWithBlocknumber = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });
    const data = await gearApi.blocks.get(valueAmount);
    console.log(data.toHuman());
  };

  const AmountInputChange = async (event: any) => {
    setValueAmount(event.target.value);
  };

  return (
    <div className="card">
      <center>Introduce blocknumber</center>
      <Input type="text" value={valueAmount} onChange={AmountInputChange} />
      <div className="horizontal">
        <Button text="Subscribe blocks" onClick={subscribeblocks} />
        <Button
          text="Get Data with BlockNumber"
          onClick={getDataWithBlocknumber}
        />
      </div>
    </div>
  );
}

export { GetDataWithBlocknumber };

import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetBlockNumber() {
  const [blocknumber, setBlocknumber] = useState<any | undefined>(0);
  const [blockhash, setBlockhash] = useState<any | undefined>("");

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getBlocknumber = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const hash = await gearApi.blocks.getBlockNumber(blockhash);
    console.log(hash.toNumber());
    setBlocknumber(hash.toNumber());
  };

  const AmountInputChange = async (event: any) => {
    setBlockhash(event.target.value);
  };

  return (
    <>
      <div className="card">
        <center>Get Blocknumber with Hash</center>
        <Input type="text" value={blockhash} onChange={AmountInputChange} />
        <div className="horizontal">
          <Button text="Subscribe blocks" onClick={subscribeblocks} />
          <Button text="Get Blocknumber" onClick={getBlocknumber} />
        </div>
      </div>
      <div className="subscribeblocks">
        <center>Blocknumber:</center>
        <center>{blocknumber}</center>
      </div>
    </>
  );
}

export {GetBlockNumber };
import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetBlockhash() {
  const [blocknumber, setBlocknumber] = useState<any | undefined>(0);
  const [blockhash, setBlockhash] = useState<any | undefined>("");

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getBlockhash = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const hash = await gearApi.blocks.getBlockHash(blocknumber);
    console.log(hash.toHex());
    setBlockhash(hash.toHex());
  };

  const AmountInputChange = async (event: any) => {
    setBlocknumber(event.target.value);
  };

  return (
    <>
      <div className="card">
        <center>Get Blockhash</center>
        <Input type="text" value={blocknumber} onChange={AmountInputChange}/>
        <div className="horizontal">
          <Button text="Subscribe blocks" onClick={subscribeblocks} />
          <Button text="Get Blockhash" onClick={getBlockhash} />
        </div>
      </div>
      <div className="subscribeblocks">
        <center>Blockhash:</center>
        <center>{blockhash}</center>
      </div>
    </>
  );
}

export { GetBlockhash };
import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetAllExtrinsics() {
  const [allextrinsics, setAllextrinsics] = useState<any | undefined>(0);
  const [blockhash, setBlockhash] = useState<any | undefined>("");

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getallextrinsics = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const extrinsics = await gearApi.blocks.getExtrinsics(blockhash);
    extrinsics.forEach((extrinsic) => {
      setAllextrinsics(extrinsic.toHuman());
      console.log(extrinsic.toHuman());
    });
  };

  const AmountInputChange = async (event: any) => {
    setBlockhash(event.target.value);
  };

  return (
    <div className="card">
      <center>Get All Extrinsics with Hash</center>
      <Input type="text" value={blockhash} onChange={AmountInputChange} />
      <div className="horizontal">
        <Button text="Subscribe blocks" onClick={subscribeblocks} />
        <Button text="Get All Extrinsics" onClick={getallextrinsics} />
      </div>
    </div>
  );
}

export { GetAllExtrinsics };
import { GearApi } from "@gear-js/api";
import { Button, Input } from "@gear-js/ui";
import { useState } from "react";

function GetAllExtrinsics() {
  const [allextrinsics, setAllextrinsics] = useState<any | undefined>(0);
  const [blockhash, setBlockhash] = useState<any | undefined>("");

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  const getallextrinsics = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const extrinsics = await gearApi.blocks.getExtrinsics(blockhash);
    extrinsics.forEach((extrinsic) => {
      setAllextrinsics(extrinsic.toHuman());
      console.log(extrinsic.toHuman());
    });
  };

  const AmountInputChange = async (event: any) => {
    setBlockhash(event.target.value);
  };

  return (
    <div className="card">
      <center>Get All Extrinsics with Hash</center>
      <Input type="text" value={blockhash} onChange={AmountInputChange} />
      <div className="horizontal">
        <Button text="Subscribe blocks" onClick={subscribeblocks} />
        <Button text="Get All Extrinsics" onClick={getallextrinsics} />
      </div>
    </div>
  );
}

export { GetAllExtrinsics };
import { GearApi } from "@gear-js/api";
import { useEffect, useState } from "react";

function SubscribeBlocks() {
  const [blocknumber, setBlocknumber] = useState();
  const [hashblock, setHashblock] = useState();

  const subscribeblocks = async () => {
    const gearApi = await GearApi.create({
      providerAddress: "wss://rpc-node.gear-tech.io",
    });

    const unsub = await gearApi.gearEvents.subscribeToNewBlocks(
      (header: any) => {
        setBlocknumber(header.number.toNumber());
        setHashblock(header.hash.toHex());
        console.log(
          `New block with number: ${header.number.toNumber()} and hash: ${header.hash.toHex()}`
        );
      }
    );
  };

  useEffect(() => {
    subscribeblocks();
  });
  return (
    <div className="subscribeblocks">
      <h3>Block Number</h3>
      <p>{blocknumber}</p>
      <h3>Block hash</h3>
      <p>{hashblock}</p>
    </div>
  );
}

export { SubscribeBlocks };
import { Button } from "@gear-js/ui";
import { GearApi, getProgramMetadata, GearKeyring  } from "@gear-js/api";
import { useAlert } from "@gear-js/react-hooks";


function  TransferEvent(){


  const alert = useAlert();


  const Transferevent= async () => {
    
    const gearApi = await GearApi.create({
        providerAddress: "wss://rpc-node.gear-tech.io",
      });
  
      const codeId =
      "0x109ffa89f6886b0ff2a8dad5c62ef45a838c78e063f998f85e0cd069b20f49dd";
    const programId =
      "0x4d47b38aa8a9b1f896283445328a43802d95f2fb413017aa90d1f668809b24d1";
  
      const somePayload = "0x676574"; // get "0x676574" inc: '0x696E63'
      const meta: any = getProgramMetadata(programId);
      const keyring = await GearKeyring.fromSuri("//Alice");

      const message: any = {
        destination: programId, // programId
        payload: somePayload,
        gasLimit: 899819245,
        value: 1000,
      };

      const extrinsic: any = gearApi.message.send(message, meta);
      await extrinsic.signAndSend(keyring, (event: any) => {
        console.log(event.toHuman());
        alert.success(Object.keys(event.toHuman().status));
      });



    // Transfer subscription
    const unsub = await gearApi.gearEvents.subscribeToTransferEvents(
        ({ data: { from, to, amount } }) => {
          console.log(`
          Transfer balance:
          from: ${from.toHex()}
          to: ${to.toHex()}
          amount: ${+amount.toString()}
          `);
        },
      );

    };




    return (

        <div className="container">
      <h1>Transfer Event</h1>
      <p className="mnemonic">Subscriptions: </p>
      <Button text="Transfer Event" onClick={Transferevent} />
    </div>
    )
}

export {TransferEvent};
import { useState } from "react";
import { ApiPromise, WsProvider } from "@polkadot/api";

import {
  web3Accounts,
  web3Enable,
  web3FromSource,
} from "@polkadot/extension-dapp";
import { useAccount, useAlert } from "@gear-js/react-hooks";
import { Button, Input, Modal } from "@gear-js/ui";

function Transfer() {

  const { account } = useAccount();
  const [status, setStatus] = useState("");

  const [isModalOpen, setIsModalOpen] = useState(false);

  const openModal = () => {
    setIsModalOpen(true);
  };

  const closeModal = () => {
    setIsModalOpen(false);
  };

  const [address, setAddress] = useState("");

  const [valueAmount, setValueAmount] = useState("");

  const alert = useAlert();

  const wsEndpoint = "wss://rpc-node.gear-tech.io";

  const transfer = async function main() {
   
    await web3Enable("my wallet");

    const allAccounts = await web3Accounts();

    const accountextension = allAccounts[0];

    const accountlocalstorage = account?.address;

    const wsProvider = new WsProvider(wsEndpoint);

    const api = await ApiPromise.create({ provider: wsProvider });

    const transferExtrinsic = api.tx.balances.transfer(
      address,
      Number(valueAmount) * 1000000000000
    );

    setValueAmount("");
    setAddress("");

    const injector = await web3FromSource(accountextension.meta.source);

    transferExtrinsic
      .signAndSend(
        accountextension.address,
        { signer: injector.signer },
        ({ status }) => {
          if (status.isInBlock) {
            console.log(
              `Completed at block hash #${status.asInBlock.toString()}`
            );
          } else {
            console.log(`Current status: ${status.type}`);

            setStatus(status.type);

            if (status.type === "Finalized") {
              alert.success(status.type);
            }
          }
        }
      )
      .catch((error: any) => {
        console.log(":( transaction failed", error);
      });
  };

  const AddressInputChange = (event: any) => {
    setAddress(event.target.value);
  };


  const AmountInputChange = (event: any) => {
    setValueAmount(event.target.value);
  };

  return (
    <>
      <Button text="Transfer" onClick={openModal} />
      {isModalOpen && (
        <Modal size="large" heading="Connect" close={closeModal}>
          <h2>Address</h2>    
          <Input type="text" value={address} onChange={AddressInputChange} />
          <p>{address}</p>
          <h2>Amount</h2>
          <Input type="text" value={valueAmount} onChange={AmountInputChange} />
          <p>{valueAmount}</p>
          <Button text="Transfer" onClick={transfer} />
        </Modal>
      )}
    </>
  );
}

export { Transfer };
import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { u8aToHex, stringToU8a } from "@polkadot/util";
import { Button } from "@gear-js/ui";

function GetCounter() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programID =
    "0x0e4b1d2684c599a55c673f54d73a518640ed048ec9f63f2fd3b997633f8b4680";

  const messageinit = "get";
  const payloadHex = u8aToHex(stringToU8a(messageinit));

  const message: any = {
    destination: programID, // programId
    payload: payloadHex,
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }

    // Usermessagesent subscribtion
    const unsub = api.gearEvents.subscribeToGearEvent(
      "UserMessageSent",
      ({
        data: {
          message: { id, source, destination, payload, value },
        },
      }) => {
        console.log(`
      messageId: ${id.toHex()}
      source: ${source.toHex()}
      payload: ${payload.toHuman()}
      `);
      }
    );
  };

  return <Button text="Send get" onClick={signer} />;
}

export { GetCounter };
import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { u8aToHex, stringToU8a } from "@polkadot/util";
import { Button } from "@gear-js/ui";

function IncCounter() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programID =
    "0x0e4b1d2684c599a55c673f54d73a518640ed048ec9f63f2fd3b997633f8b4680";
  
  const messageinit = "inc";
  const payloadHex=u8aToHex(stringToU8a(messageinit ));
 
  const message: any = {
    destination: programID, // programId
    payload: payloadHex,
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }

    // Usermessagesent subscribtion
    const unsub = api.gearEvents.subscribeToGearEvent(
      "UserMessageSent",
      ({
        data: {
          message: { id, source, destination, payload, value },
        },
      }) => {
        console.log(`
      messageId: ${id.toHex()}
      source: ${source.toHex()}
      payload: ${payload.toHuman()}
      `);
      }
    );
  };

  return <Button text="GetCounter" onClick={signer} />;
}

export { IncCounter };

import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { u8aToHex, stringToU8a } from "@polkadot/util";
import { Button } from "@gear-js/ui";

function DecCounter() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programID =
    "0x0e4b1d2684c599a55c673f54d73a518640ed048ec9f63f2fd3b997633f8b4680";
  
  const messageinit = "dec";
  const payloadHex=u8aToHex(stringToU8a(messageinit ));
 
  const message: any = {
    destination: programID, // programId
    payload: payloadHex,
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }

    // Usermessagesent subscribtion
    const unsub = api.gearEvents.subscribeToGearEvent(
      "UserMessageSent",
      ({
        data: {
          message: { id, source, destination, payload, value },
        },
      }) => {
        console.log(`
      messageId: ${id.toHex()}
      source: ${source.toHex()}
      payload: ${payload.toHuman()}
      `);
      }
    );
  };

  return <Button text="GetCounter" onClick={signer} />;
}

export { DecCounter };
import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";

function MintButton() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programIDFT =
    "0xb4c89824de15e0784990633e3c29564ae217234035df35dc4b0fefd265d2e691";
  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const metadata = getProgramMetadata("0x" + meta);

  const message: any = {
    destination: programIDFT, // programId
    payload: { mint: 1000 },
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message, metadata);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }
  };

  return <Button text="Mint" onClick={signer} />;
}

export { MintButton };
import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";

function BurnButton() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programIDFT =
    "0xb4c89824de15e0784990633e3c29564ae217234035df35dc4b0fefd265d2e691";
  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const metadata = getProgramMetadata("0x" + meta);

  const message: any = {
    destination: programIDFT, // programId
    payload: { burn: 100 },
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message, metadata);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }
  };

  return <Button text="Burn" onClick={signer} />;
}

export { BurnButton }; import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { decodeAddress, getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";

function TransferButton() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programIDFT =
    "0xb4c89824de15e0784990633e3c29564ae217234035df35dc4b0fefd265d2e691";
  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const metadata = getProgramMetadata("0x" + meta);

  const message: any = {
    destination: programIDFT, // programId
    payload: {
      transfer: [
        decodeAddress("5FCZdMxjrG8DKnu19roVMaDjpPSsbv3mkrJVbyB2jrjbAZbD"),
        decodeAddress("5HB7N9FqBFbrJKguyx3D8L61auaLLDrARiKaJuUryrPg2NvG"),
        10,
      ],
    },
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message, metadata);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }
  };

  return <Button text="Send Message and sing" onClick={signer} />;
}

export { TransferButton };
import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { decodeAddress, getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";

function ApproveButton() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programIDFT =
    "0xb4c89824de15e0784990633e3c29564ae217234035df35dc4b0fefd265d2e691";
  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const metadata = getProgramMetadata("0x" + meta);

  const message: any = {
    destination: programIDFT, // programId
    payload: {
      approve: [
        decodeAddress("5FCZdMxjrG8DKnu19roVMaDjpPSsbv3mkrJVbyB2jrjbAZbD"),
        20,
      ],
    },
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message, metadata);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }
  };

  return <Button text="Approve" onClick={signer} />;
}

export { ApproveButton }; import { useAccount, useApi, useAlert } from "@gear-js/react-hooks";
import { web3FromSource } from "@polkadot/extension-dapp";
import { getProgramMetadata } from "@gear-js/api";
import { Button } from "@gear-js/ui";

function TotalSupply() {
  const alert = useAlert();
  const { accounts, account } = useAccount();
  const { api } = useApi();

  const programIDFT =
    "0xb4c89824de15e0784990633e3c29564ae217234035df35dc4b0fefd265d2e691";
  const meta =
    "0100000000000103000000010700000000000000000108000000a90b3400081466745f696f28496e6974436f6e66696700000c01106e616d65040118537472696e6700011873796d626f6c040118537472696e67000120646563696d616c73080108753800000400000502000800000503000c081466745f696f204654416374696f6e000118104d696e74040010011075313238000000104275726e040010011075313238000100205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380002001c417070726f7665080108746f14011c4163746f724964000118616d6f756e74100110753132380003002c546f74616c537570706c790004002442616c616e63654f66040014011c4163746f724964000500001000000507001410106773746418636f6d6d6f6e287072696d6974697665731c4163746f724964000004001801205b75383b2033325d0000180000032000000008001c081466745f696f1c46544576656e74000110205472616e736665720c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380000001c417070726f76650c011066726f6d14011c4163746f724964000108746f14011c4163746f724964000118616d6f756e74100110753132380001002c546f74616c537570706c790400100110753132380002001c42616c616e63650400100110753132380003000020081466745f696f3c496f46756e6769626c65546f6b656e00001801106e616d65040118537472696e6700011873796d626f6c040118537472696e67000130746f74616c5f737570706c791001107531323800012062616c616e6365732401505665633c284163746f7249642c2075313238293e000128616c6c6f77616e6365732c01905665633c284163746f7249642c205665633c284163746f7249642c2075313238293e293e000120646563696d616c730801087538000024000002280028000004081410002c00000230003000000408142400";

  const metadata = getProgramMetadata("0x" + meta);

  const message: any = {
    destination: programIDFT, // programId
    payload: "totalSupply",
    gasLimit: 899819245,
    value: 0,
  };

  const signer = async () => {
    const localaccount = account?.address;
    const isVisibleAccount = accounts.some(
      (visibleAccount) => visibleAccount.address === localaccount
    );

    if (isVisibleAccount) {
      // Create a message extrinsic
      const transferExtrinsic = api.message.send(message, metadata);

      const injector = await web3FromSource(accounts[0].meta.source);

      transferExtrinsic
        .signAndSend(
          accounts[0].address,
          { signer: injector.signer },
          ({ status }) => {
            if (status.isInBlock) {
              console.log(
                `Completed at block hash #${status.asInBlock.toString()}`
              );
              alert.success(`Block hash #${status.asInBlock.toString()}`);
            } else {
              console.log(`Current status: ${status.type}`);
              if (status.type === "Finalized") {
                alert.success(status.type);
              }
            }
          }
        )
        .catch((error: any) => {
          console.log(":( transaction failed", error);
        });
    } else {
      alert.error("Account not available to sign");
    }
  };

  return <Button text="Total Supply" onClick={signer} />;
}

export { TotalSupply };


export { GetTimestamp };
mkdir Servidor
cd Servidor
touch Servidor.py
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def index():
    return jsonify({'value': True})  # Cambia el valor booleano según tus necesidades

if __name__ == '__main__':
    app.run(debug=True)
python Servidor.py
  .
  . 
  "private": true,
  "proxy": "http://127.0.0.1:5000/",// Esta linea es la que se agrega
  "dependencies": {
   .
   .
  }
  import { useEffect, useState } from 'react';

function Request () {
  const [value, setValue] = useState<boolean | null>(null);

  
 const request = async function fetchData() {
    try {
      const response = await fetch('http://localhost:5000/');
      const data = await response.json();
      setValue(data.value);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }

  return (
    <div>
      <h1>Valor booleano: {value === null ? 'Cargando...' : value.toString()}</h1>
      <button onClick={request}/>
    </div>
  );
}

export { Request } ;

from flask import Flask, jsonify
from flask_cors import CORS
import requests

app = Flask(__name__)

CORS(app)

@app.route('/')
def index():
    return jsonify({'value': True})  

@app.route('/request')  # Así creamos una nueva ruta para responder la petición 
def request():
    try:
        response = requests.get('https://api.coingecko.com/api/v3/ping')  # Reemplaza con la URL de la API que deseas consultar, en este ejemplo usaremos coingecko
        data = response.json()
        return jsonify(data)
    except requests.exceptions.RequestException as e:
        return jsonify({'error': str(e)})



if __name__ == '__main__':
    app.run(debug=True)

