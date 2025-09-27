{"@context": ["https://www.w3.org/2018/credentials/v1", "https://w3id.org/blockcerts/v3"], "type": ["VerifiableCredential", "BlockcertsCredential"], "issuer": "https://www.blockcerts.org/samples/2.0/issuer-testnet.json", "issuanceDate": "*|DATE|*", "id": "urn:uuid:*|CERTUID|*", "credentialSubject": {"id": "ecdsa-koblitz-pubkey:*|PUBKEY|*", "alumniOf": {"id": "https://www.issuer.org"}}}
Structure du certificat :

@context --> tjr présent dans les VC (définition du vocab utilisé) --> ne pas changer (sauf si on change de blockcerts)
type --> défini type de certif (ne pas changer)
issuer : pointe vers un doc JSON qui décrit l'issuer (ex : https://university.edu/issuer.json)
DAte : 2025-09-27T12:00:00Z (format ISO8601)
id : identifiant unique du contrat (UUID), généré pour chaque certificat
credentialsubjet : décrit le bénéficiaire (public key : pour rester certifié blockcerts et avoir l'interopérabilité, il faut que chaque receveur aie une clé donc un wallet)
Warning : pour le champ pubkey officiel de Blockcerts V3 --> préfix ecdsa-koblitz-pubkey (utilsé par les déchiffreurs blockcerts pour repérer les clés publiques). Voir pk ce préfix là si curieux.
IMPORTANT : il manque ici une potentielle clé "proof" qui contient un hash (on peut penser au hash du diplome pour le use-case diplomes)
IMPORTANT : Il manque aussi le nonce, qui est obligatoire dans le contract pour BlockCerts V3 (certs tool peut le générer en auto, mais en PROD juste on sort un UUID random via python)
P.S : Ce nonce de blockcerts dans le contract n'a rien a voir avec le nonce de la blockchain, le nonce de transaction
Exemple : 
"proof": {
  "type": "MerkleProof2019",
  "created": "2025-09-26T12:00:00Z",
  "proofValue": "0xabc123...", (hash du diplome du candidat i dans le merkle tree) 
  "proofPurpose": "assertionMethod",
  "verificationMethod": "https://escp.fr/blockcerts/issuer-profile.json#key-1" (ou un autre systeme de verif publique)
}
Pour ajouter ça, il faut donc l'ajouter dans le template (test.json)

Comment gérer cette création nécessaire de wallet pour les bénéficiaires ?
1) Envoyer mail / mener à une plateforme pour créer son wallet (juste couple publique/privée), PUIS faire ré-envoyer la clé publique pour envoi
2) Générer le wallet directement à la création donc sur serveur de l'issuer 
3) Préparer l'envoi, le garder sur les serveurs de l'issuer. Envoyer l'email etc ou autre. Attendre l'arrivée d'une clé publique correspondante sur un certain endpoint, et faire l'envoie sur BC automatiquement.


usage: create_v3_certificate_template.py [-h] 
                                         [-c MY_CONFIG] 
                                         [--data_dir DATA_DIR] 
                                         [--issuer_url ISSUER_URL] --issuer_id ISSUER_ID [--template_dir TEMPLATE_DIR] 
                                         [--template_file_name TEMPLATE_FILE_NAME]
                                         [--additional_global_fields ADDITIONAL_GLOBAL_FIELDS] 
                                         [--additional_per_recipient_fields ADDITIONAL_PER_RECIPIENT_FIELDS]
Tous les "--" on les rempli dans conf.ini
Et donc on peut créer des templates de certificats ainsi.