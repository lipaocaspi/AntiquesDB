# AntiquesDB

#### DDL

```sql
SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema antiquesDB
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema antiquesDB
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `antiquesDB` DEFAULT CHARACTER SET utf8 ;
USE `antiquesDB` ;

-- -----------------------------------------------------
-- Table `antiquesDB`.`user`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`user` (
  `id_user` VARCHAR(15) NOT NULL,
  `name_user` VARCHAR(20) NOT NULL,
  `password_user` VARCHAR(45) NOT NULL,
  `fullname_user` VARCHAR(45) NOT NULL,
  `dateofregistration_user` DATE NOT NULL,
  `role_user` ENUM('Vendedor', 'Comprador', 'Administrador') NOT NULL,
  `phone_user` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id_user`),
  UNIQUE INDEX `id_user_UNIQUE` (`id_user` ASC) VISIBLE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`country`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`country` (
  `id_country` VARCHAR(5) NOT NULL,
  `name_country` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`id_country`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`region`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`region` (
  `id_region` VARCHAR(5) NOT NULL,
  `name_region` VARCHAR(45) NOT NULL,
  `id_country` VARCHAR(5) NOT NULL,
  PRIMARY KEY (`id_region`),
  INDEX `fk_region_country_idx` (`id_country` ASC) VISIBLE,
  CONSTRAINT `fk_region_country`
    FOREIGN KEY (`id_country`)
    REFERENCES `antiquesDB`.`country` (`id_country`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB
COMMENT = '		';


-- -----------------------------------------------------
-- Table `antiquesDB`.`city`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`city` (
  `id_city` VARCHAR(5) NOT NULL,
  `name_city` VARCHAR(45) NOT NULL,
  `id_region` VARCHAR(5) NOT NULL,
  PRIMARY KEY (`id_city`),
  INDEX `fk_city_region1_idx` (`id_region` ASC) VISIBLE,
  CONSTRAINT `fk_city_region1`
    FOREIGN KEY (`id_region`)
    REFERENCES `antiquesDB`.`region` (`id_region`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`address`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`address` (
  `id_address` INT NOT NULL AUTO_INCREMENT,
  `detail_address` VARCHAR(50) NOT NULL,
  `postalcode_address` VARCHAR(10) NOT NULL,
  `id_city` VARCHAR(5) NOT NULL,
  `id_user` VARCHAR(15) NOT NULL,
  PRIMARY KEY (`id_address`),
  INDEX `fk_address_city1_idx` (`id_city` ASC) VISIBLE,
  INDEX `fk_address_user1_idx` (`id_user` ASC) VISIBLE,
  CONSTRAINT `fk_address_city1`
    FOREIGN KEY (`id_city`)
    REFERENCES `antiquesDB`.`city` (`id_city`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_address_user1`
    FOREIGN KEY (`id_user`)
    REFERENCES `antiquesDB`.`user` (`id_user`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`category`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`category` (
  `id_category` INT NOT NULL AUTO_INCREMENT,
  `name_category` VARCHAR(20) NOT NULL,
  `description_category` VARCHAR(100) NULL,
  PRIMARY KEY (`id_category`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`antique`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`antique` (
  `id_antique` INT NOT NULL AUTO_INCREMENT,
  `name_antique` VARCHAR(20) NOT NULL,
  `description_antique` VARCHAR(45) NOT NULL,
  `period_antique` VARCHAR(20) NOT NULL,
  `state_antique` ENUM('Excelente', 'Bueno', 'Regular') NOT NULL,
  `price_antique` DOUBLE NOT NULL,
  `picture_antique` VARCHAR(100) NOT NULL,
  `dateofsale_antique` DATE NOT NULL,
  `search_antique` INT NOT NULL DEFAULT 0,
  `id_category` INT NOT NULL,
  `id_country` VARCHAR(5) NOT NULL,
  `id_user` VARCHAR(15) NOT NULL,
  PRIMARY KEY (`id_antique`),
  UNIQUE INDEX `id_antique_UNIQUE` (`id_antique` ASC) VISIBLE,
  INDEX `fk_antique_category1_idx` (`id_category` ASC) VISIBLE,
  INDEX `fk_antique_country1_idx` (`id_country` ASC) VISIBLE,
  INDEX `fk_antique_user1_idx` (`id_user` ASC) VISIBLE,
  CONSTRAINT `fk_antique_category1`
    FOREIGN KEY (`id_category`)
    REFERENCES `antiquesDB`.`category` (`id_category`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_antique_country1`
    FOREIGN KEY (`id_country`)
    REFERENCES `antiquesDB`.`country` (`id_country`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_antique_user1`
    FOREIGN KEY (`id_user`)
    REFERENCES `antiquesDB`.`user` (`id_user`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`payment_method`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`payment_method` (
  `id_payment_method` INT NOT NULL AUTO_INCREMENT,
  `name_payment_method` VARCHAR(30) NOT NULL,
  PRIMARY KEY (`id_payment_method`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`transaction`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`transaction` (
  `id_transaction` INT NOT NULL AUTO_INCREMENT,
  `price_transaction` DOUBLE NOT NULL,
  `date_transaction` DATE NOT NULL,
  `status_transaction` ENUM('En proceso', 'Enviado', 'Entregado') NOT NULL,
  `id_buyer` VARCHAR(15) NOT NULL,
  `id_antique` INT NOT NULL,
  `id_payment_method` INT NOT NULL,
  PRIMARY KEY (`id_transaction`),
  INDEX `fk_transaction_user1_idx` (`id_buyer` ASC) VISIBLE,
  INDEX `fk_transaction_antique1_idx` (`id_antique` ASC) VISIBLE,
  INDEX `fk_transaction_payment_method1_idx` (`id_payment_method` ASC) VISIBLE,
  CONSTRAINT `fk_transaction_user1`
    FOREIGN KEY (`id_buyer`)
    REFERENCES `antiquesDB`.`user` (`id_user`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_transaction_antique1`
    FOREIGN KEY (`id_antique`)
    REFERENCES `antiquesDB`.`antique` (`id_antique`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_transaction_payment_method1`
    FOREIGN KEY (`id_payment_method`)
    REFERENCES `antiquesDB`.`payment_method` (`id_payment_method`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`activity_log`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`activity_log` (
  `id_activity_log` INT NOT NULL AUTO_INCREMENT,
  `typeactivity_activity_log` VARCHAR(50) NOT NULL,
  `date_activity_log` DATETIME NOT NULL,
  `id_user` VARCHAR(15) NOT NULL,
  PRIMARY KEY (`id_activity_log`),
  INDEX `fk_activity_log_user1_idx` (`id_user` ASC) VISIBLE,
  CONSTRAINT `fk_activity_log_user1`
    FOREIGN KEY (`id_user`)
    REFERENCES `antiquesDB`.`user` (`id_user`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `antiquesDB`.`inventory`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `antiquesDB`.`inventory` (
  `id_inventory` INT NOT NULL AUTO_INCREMENT,
  `stock_inventory` INT NOT NULL,
  `status_inventory` ENUM('Disponible', 'Vendido', 'Retirado') NOT NULL,
  `dateupdate_inventory` DATE NOT NULL,
  `id_antique` INT NOT NULL,
  PRIMARY KEY (`id_inventory`),
  INDEX `fk_inventory_antique1_idx` (`id_antique` ASC) VISIBLE,
  CONSTRAINT `fk_inventory_antique1`
    FOREIGN KEY (`id_antique`)
    REFERENCES `antiquesDB`.`antique` (`id_antique`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

