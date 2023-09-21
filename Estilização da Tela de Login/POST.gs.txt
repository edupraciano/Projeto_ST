// Constantes Globais

const SS = SpreadsheetApp.openById(
  "1XT34dl9qhguB8dRjlm3JhmpmqiLs_UpRXjrDgEmmcV0"
);
const main = SS.getSheetByName("main");
const usuarios = SS.getSheetByName("usuários");
/////////////////////////////////////////////////////////////////////////////////

function doPost(e) {
  if (e.parameters.qual_funcao == "entrada_de_estabelecimento") {
    if (autenticacao(e)) {
      return ContentService.createTextOutput(
        JSON.stringify(entrada_de_estabelecimento(e))
      );
    }
  } else if (e.parameters.qual_funcao == "ler_estabelecimento") {
    if (autenticacao(e)) {
      return ContentService.createTextOutput(
        JSON.stringify(ler_estabelecimento(e))
      );
    }
  } else if (e.parameters.qual_funcao == "remover_estabelecimento") {
    if (autenticacao(e)) {
    }
    remover_estabelecimento(e);
  } else if (e.parameters.qual_funcao == "autenticacao") {
    return ContentService.createTextOutput(JSON.stringify(autenticacao(e)));
  }
}

function autenticacao(e) {
  let ultima_linha_usuarios = usuarios.getLastRow();

  let user_name = e.parameters.user_name;
  let password = e.parameters.password;

  let usuario_autenticado = false;
  // usuario_autenticado = "Usuário Não Autenticado."

  for (let i = 1; i <= ultima_linha_usuarios; i++) {
    if (usuarios.getRange(i, 1).getValue() == user_name) {
      if (usuarios.getRange(i, 2).getValue() == password) {
        usuario_autenticado = true;
        //usuario_autenticado = "Usuário Autenticado."
      }
    }
  }
  // console.log(usuario_autenticado);
  return usuario_autenticado;
}

function entrada_de_estabelecimento(e) {
  try {
    var SS = SpreadsheetApp.openById(
      "1XT34dl9qhguB8dRjlm3JhmpmqiLs_UpRXjrDgEmmcV0"
    );
    var main = SS.getSheetByName("main");
    var data_do_cadastro = Utilities.formatDate(
      new Date(),
      "GMT-3",
      "dd/MM/yyyy"
    );

    var colunaId = main.getRange("A2:A").getValues();
    var id = "";

    if (id == "") {
      id = Math.max.apply(null, colunaId) + 1;
    }

    if (e.parameters.id_do_estabelecimento == "") {
      // ADICIONAR ESTABELECIMENTO
      var ultimaLinha = main.getLastRow() + 1;

      main.getRange(ultimaLinha, 1).setValue(id);
      main
        .getRange(ultimaLinha, 2)
        .setValue(e.parameters.nome_do_estabelecimento);
      main.getRange(ultimaLinha, 3).setValue(e.parameters.produto);
      main.getRange(ultimaLinha, 4).setValue(e.parameters.chave);
      main.getRange(ultimaLinha, 5).setValue(e.parameters.maquina);
      main.getRange(ultimaLinha, 6).setValue(e.parameters.endereco);
      main.getRange(ultimaLinha, 7).setValue(e.parameters.responsavel);
      main.getRange(ultimaLinha, 8).setValue(e.parameters.contato);
      main.getRange(ultimaLinha, 9).setValue(data_do_cadastro);
      return "Estabelecimento Adicionado com Sucesso!";
    } else {
      // ALTERAR ESTABELECIMENTO
      var ultimaLinha = main.getLastRow() + 1;

      for (var i = 1; i < ultimaLinha; i++) {
        if (
          main.getRange(i, 1).getValue() == e.parameters.id_do_estabelecimento
        ) {
          main.getRange(i, 1).setValue(e.parameters.id_do_estabelecimento);
          main.getRange(i, 2).setValue(e.parameters.nome_do_estabelecimento);
          main.getRange(i, 3).setValue(e.parameters.produto);
          main.getRange(i, 4).setValue(e.parameters.chave);
          main.getRange(i, 5).setValue(e.parameters.maquina);
          main.getRange(i, 6).setValue(e.parameters.endereco);
          main.getRange(i, 7).setValue(e.parameters.responsavel);
          main.getRange(i, 8).setValue(e.parameters.contato);
          main.getRange(i, 9).setValue(data_do_cadastro);
          return "Estabelecimento Aletrado com Sucesso!";
        }
      }
    }
  } catch (e) {
    return "Houve algum problema!";
  }
}

function ler_estabelecimento(e) {
  var ultimaLinha = main.getLastRow();
  var ultimaColuna = main.getLastColumn();
  var todaATabela = main
    .getRange(2, 1, ultimaLinha - 1, ultimaColuna)
    .getValues();

  return todaATabela;
}

function remover_estabelecimento(e) {
  let ultima_linha_main = main.getLastRow();
  for (var i = 1; i <= ultima_linha_main; i++) {
    if (main.getRange(i, 1).getValue() == e.parameters.id_do_estabelecimento) {
      main.deleteRow(i);
      break;
    }
  }
}
