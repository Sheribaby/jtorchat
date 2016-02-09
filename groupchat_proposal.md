This is a complex concept to do. These are the elements I currenly can think of for groupchat. Please edit your ideas here (or comment below)

RSA signiture verification - for spoof protection

Groupchat Broadcast


---


# Proposed Group Chat process #

Legend(key):
```
UUID  -(e.g. KHIGIKJ) unique id string that uniquely identifies the chat group 
UserA -(e.g. Andrew) group starter's torchat address
UserB -(e.g. Bokkaa) group participant's torchat address
UserC -(e.g. Smitho) group participant's torchat address (leaves later)
UserD -(e.g. Vandea) group participant's torchat address (joins later)
Sig   - RSA message verification data (for anti-spoofing)
```

UserA wants to start a group, so creates a new string with starting information about the group. Lets call it "GroupInfo"

```
initial structure of groupInfo:
groupInfo = "UUID UserA [UserB] [UserC]";
e.g.
groupInfo = "KHIGIKJ uemrifkolgutjbuq egumkihjfujropid gh8jdiek24ngei35";
```

## Steps for groupchat ##

  * Initial: UserA sends groupInfo to UserB and UserC
  * Initial2: Everybody grabs everybody's RSA public key
  * OnUserMsg: The user sends a broadcast msg structured like this:
    1. send: [command](command.md) [UUID](UUID.md) [Sig](Sig.md) [Msg](Msg.md)
    1. send: "groupChat KHIGIKJ [Sig](Sig.md) hey all! How are you"
  * OnRelayMsg: Only relay message to those in the group
  * OnReceive: Verify Sig, then display message. if message is unverified/error, then add (unverified) to name.
  * OnLeave: Send a command to all to delete you from list.
    1. send: [command](command.md) [UUID](UUID.md) [Sig](Sig.md) [Msg](leave.md)
    1. send: "groupchat KHIGIKJ [Sig](Sig.md) \leave"
  * OnAdd: Send everyone the user's ID
    1. send: [command](command.md) [UUID](UUID.md) [Sig](Sig.md) [Msg](add.md)
    1. send: "groupchat KHIGIKJ [Sig](Sig.md) \add gh8jdiek24ngei35"


---


Can possibly set chat group to disallow adding users by nonstarter (by default? maybe not)

if that is the case, we shall also need

  * OnKick:
    1. send: [command](command.md) [UUID](UUID.md) [Sig](Sig.md) [Msg](add.md)
    1. send: "groupchat KHIGIKJ [Sig](Sig.md) \kick gh8jdiek24ngei35"

as well as a way to 'flag' a chatgroup as moderated, e.g. adding an "@" in front of an address, to show that this person is an admin.