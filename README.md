# codeigniter-datatable-helper
Codeigniter Helper class for jquery data table 

Put this file inside helper folder of CI and thats it.

Add datatable_helper in /config/autoload.php
like: 
$autoload['helper'] = array('url','file','email','common_helper', 'datatable_helper');

Sample controler function:

public function deal_list()
{
		$post = $this->input->post();

		$columns = array(
			array( 'db' => 'db_uniqueid',  'dt' => 0 ),
			array( 'db' => 'do_offertitle',  'dt' => 1 ),
			array( 'db' => 'de_name',  'dt' => 2 ),
			array( 'db' => 'du_uname',  'dt' => 3 ),
			array( 'db' => 'dd_validtilldate',  
					'dt' => 4 ,
					'formatter' => function( $d, $row ) {
						return date( 'jS M y', strtotime($d));
					}
			),
			array( 'db' => 'db_amntpaid',  'dt' => 5 ),
			array('db'        => 'db_date',
					'dt'        => 6,
					'formatter' => function( $d, $row ) {
						return date( 'jS M y', strtotime($d));
					}
			),
			array( 'db' => 'CONCAT(db_autoid,"|",db_dealstatus)',
					'dt' => 7,
					'formatter' => function( $d, $row ) {
						list($id,$status) = explode("|",$d);
						return '<a href="javascript:void(0);" data-db_autoid="'.$id.'" class="fa fa-eye deal-buy-status '.$status.'"  title="'.$status.'" alt="'.$status.'"></a>';
					},
					"sort" => "db_dealstatus"
			),
		);
		$join1 = array(DEAL_USER,'du_autoid = db_uid');
		$join2 = array(DEAL_DETAIL,'dd_autoid = db_dealid');
		$join3 = array(DEAL_DEALER,'de_autoid = dd_dealerid');
		$join4 = array(DEAL_OFFER,'do_autoid = db_offerid');
		$custom_where = array();
		
		if($this->user_session['role'] == 'd') {
			if(!$this->user_session['dealer_info'])
				$custom_where = array('dd_dealerid'=>0);
			else
				$custom_where = array('dd_dealerid'=>$this->user_session['dealer_info']->de_autoid);
		}	
		
		echo json_encode( SSP::simple( $post, DEAL_BUYOUT, "db_autoid", $columns ,array($join1, $join2, $join3,$join4),$custom_where) );exit;

}


You also can assign alias of coloumn name if join table hase same coloumn name 

      $columns = array(
			  array( 'alias' => 'cat_name','db' => 'c.name', 'dt' => 0 ),
			  array( 'db' => 'p.brand',  'dt' => 1 ),
			  array( 'db' => 'p.name',  'dt' => 2 ),
		    ...
		  );



