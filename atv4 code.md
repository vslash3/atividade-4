# atividade-4
	delimiter $$
create trigger tri_vendas_ai
after insert on comivenda
for each row
begin
	## declaro as variáveis que utilizarei
  close busca_itens;
    
    SET vtotal_item = NEW.n_valoivenda * NEW.n_qtdeivenda;
    
	UPDATE comvenda SET n_totavenda = vtotal_itens - vtotal_item
	WHERE n_numevenda = new.n_numevenda;
end$$
delimiter ;clare vtotal_itens float(10,2) DEFAULT 0;
	declare vtotal_item float(10,2) DEFAULT 0;
	declare total_item float(10,2);
    DECLARE qtd_item INT DEFAULT 0;
    DECLARE fimloop INT DEFAULT 0;
    
	## cursor para buscar os itens já registrados da venda
	declare busca_itens cursor for
		select n_valoivenda, n_qtdeivenda
		from comivenda
		where n_numevenda = new.n_numevenda;
    
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET fimloop = 1;
    
	## abro o cursor
	open busca_itens;
		## declaro e inicio o loop
		itens : loop
            
			IF fimloop = 1 THEN
				LEAVE itens;
			END IF;
        
			fetch busca_itens into total_item, qtd_item;
			
			## somo o valor total dos itens (produtos)
			SET vtotal_item = total_item * qtd_item;
			set vtotal_itens = vtotal_itens + vtotal_item;
            
		end loop itens;
CLOSE busca_itens;
    SET vtotal_item = NEW.n_valoivenda * NEW.n_qtdeivenda;
	UPDATE comvenda SET n_totavenda = vtotal_itens - vtotal_item
	WHERE n_numevenda = NEW.n_numevenda;
END$$
DELIMITER ;
