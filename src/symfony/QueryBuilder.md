Repository

	$qb = $this->createQueryBuilder();
	$expr = $qb->expr();
	$qb->select('DISTINCT itc.item_id')
	    ->from('items_to_collections', 'itc')
	    ->innerJoin('itc', 'statuses', 's', 's.id = itc.status_id')
	    ->innerJoin('itc', 'tags_to_items', 'tti', 'tti.item_id = itc.item_id')
	    ->where($expr->andX(
	        $expr->eq('s.status_symbol', ':status_symbol'),
	        $expr->eq('tti.tag_id', ':tag_id'),
	        $expr->like('itc.path', ':path')
	    ))
	    ->setParameters([
	        'status_symbol' => 'st_live',
	        'tag_id' => $tag_id,
	        'path' => $parent_path
	    ])
	    ->orderBy('itc.' . $sort_order, 'ASC');
	if ($limit > 0) {
	    $qb->setMaxResults($limit);
	}
	if($offset > 0){
	    $qb->setFirstResult($offset);   
	}
	if (false === empty($existing)) {
	    $qb->andWhere($expr->notIn('itc.item_id', ':item_id'))
	        ->setParameter('item_id', ((array) $existing), $dc::PARAM_STR_ARRAY);
	}
	$items_ids = $qb->execute()->fetchAll(\PDO::FETCH_COLUMN);

LIKE  

	$qb = $this->createQueryBuilder('u');
	$qb->where(
	         $qb->expr()->like('u.username', ':user')
	     )
	     ->setParameter('user','%Andre%')
	     ->getQuery()
	     ->getResult();

IN
	
	$qb = $this->createQueryBuilder('u');
    $qb->where($qb->expr()->in('u.id', ':ids'));
    //$qb->add('where', $qb->expr()->in('u.id', ':ids'));
    $qb->setParameter('ids', $ids);
    return $qb->getQuery()->getArrayResult();

orx  

	$conditions = array('e.type = x', 'e.type = Y', 'e.type = N');
	
	$orX = $qb->expr()->orX();
	$orX->addMultiple($conditions);
	
	$qb->where($orX);

SELECT  

	$qb->select(array('u')) // string 'u' is converted to array internally
	   ->from('User', 'u')
	   ->where($qb->expr()->orX(
	       $qb->expr()->eq('u.id', '?1'),
	       $qb->expr()->like('u.nickname', '?2')
	   ))
	   ->orderBy('u.surname', 'ASC');

UPDATE

	$qb = $this->em->createQueryBuilder();
	$q = $qb->update('models\User', 'u')
	        ->set('u.username', '?1')
	        ->set('u.email', '?2')
	        ->where('u.id = ?3')
	        ->setParameter(1, $username)
	        ->setParameter(2, $email)
	        ->setParameter(3, $editId)
	        ->getQuery();
	$p = $q->execute();

return array result  


	$result = $this->getDoctrine()
               ->getRepository('MyBundle:MyEntity')
               ->createQueryBuilder('e')
               ->select('e')
               ->getQuery()
               ->getResult(\Doctrine\ORM\Query::HYDRATE_ARRAY);

	public function getHeaderNav($moduleId, $toArray = false, $ids = null)
    {
        $qb = $this->createQueryBuilder('r');
        $qb->where($qb->expr()
            ->andX($qb->expr()
            ->eq('r.status', ':status'), $qb->expr()
            ->eq('r.pid', ':pid'), $qb->expr()
            ->isNotNull('r.icon')))
            ->setParameters([
            'status' => 1,
            'pid' => $moduleId
        ]);
        if(!empty($ids)){
            $qb->andWhere('r.id IN(:ids)')->setParameter('ids', $ids);
        }
        
        if ($toArray) {
            return $qb->getQuery()->getResult(\Doctrine\ORM\Query::HYDRATE_ARRAY);
			// or 
			return $qb->getQuery()->getArrayResult();
        } else {
            return $qb->getQuery()->getResult();
        }
    }