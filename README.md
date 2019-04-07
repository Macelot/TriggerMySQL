# TriggerMySQL
Exemplos de Trigger no MySQL
CREATE  TABLE IF NOT EXISTS `receita` (
  `idReceita` BIGINT(20) NOT NULL AUTO_INCREMENT ,
  `descricao` VARCHAR(60) NOT NULL ,
  `quantidadeResultante` INT(11) NOT NULL ,
  PRIMARY KEY (`idReceita`))
ENGINE = InnoDB;

CREATE  TABLE IF NOT EXISTS `saldoAlimento` (
  `idSaldoAlimento` INT(11) NOT NULL AUTO_INCREMENT ,
  `quantidadeFabricada` int(11) NOT NULL ,
  `idReceitaFK` BIGINT(20) NOT NULL ,
  `dataCadastro` DATETIME NOT NULL ,
  PRIMARY KEY (`idSaldoAlimento`))
ENGINE = InnoDB;

CREATE  TABLE IF NOT EXISTS `fabricacao` (
  `idFabricacao` BIGINT(20) NOT NULL AUTO_INCREMENT ,
  `idReceitaFK` BIGINT(20) NOT NULL ,
  `dataFabricacao` DATE NOT NULL ,
  PRIMARY KEY (`idFabricacao`))
ENGINE = InnoDB;


CREATE  TABLE IF NOT EXISTS `saldoIngrediente` (
  `idSaldoIngrediente` INT(11) NOT NULL AUTO_INCREMENT ,
  `quantidadeUtilizada` BIGINT(20) NOT NULL ,
  `idIngredienteFK` BIGINT(20) NOT NULL 
  PRIMARY KEY (`idSaldoIngrediente`))
ENGINE = InnoDB;

CREATE  TABLE IF NOT EXISTS `itensIngredienteReceita` (
  `idItemIngredienteReceita` INT(11) NOT NULL AUTO_INCREMENT ,
  `idReceitaFK` BIGINT(20) NOT NULL ,
  `idIngredienteFK` BIGINT(20) NOT NULL ,
  PRIMARY KEY (`idItemIngredienteReceita`))
ENGINE = InnoDB

Criand um gatilho, para ocorrer depois de uma inserção na tabela “fabricacao”. 
Atente-se ao fato de que os seguintes valores serão informados no momento de uma inserção na tabela “fabricacao”:
idReceitaFK
dataFabricacao
Estes dois valores serão tratados como NEW.

O gatilho deve inserir um registro na tabela “saldoAlimento”, com os seguintes valores:

dataCadastro = ao mesmo valor informado em dataFabricacao da tabela “fabricacao” (NEW) 
idReceitaFK = ao mesmo valor informado em idReceitaFK da tabela “fabricacao” (NEW)
quantidadeFabricada =  ao valor retornado pela busca:
SELECT quantidadeResultante FROM receita WHERE idReceita = NEW.idReceitaFK;

DELIMITER $$ 
CREATE TRIGGER depoisInsertFabricacao
AFTER INSERT ON fabricacao 
FOR EACH ROW BEGIN 
INSERT INTO saldoAlimento (quantidadeFabricada,idReceitaFK,dataCadastro) 
VALUES (
(SELECT quantidadeResultante FROM receita where idReceita=NEW.idReceitaFK),
NEW.idReceitaFK,
NEW.dataFabricacao
); 
END$$ 
DELIMITER ;

Ou
Criar a procedure:
SELECT quantidadeResultante into resp FROM receita WHERE idReceita=id

BEGIN 
call quantResultante2(NEW.idReceitaFK,@teste);
INSERT INTO saldoAlimento (quantidadeFabricada,idReceitaFK,dataCadastro) 
VALUES (
@teste,
NEW.idReceitaFK,
NEW.dataFabricacao
); 
END

