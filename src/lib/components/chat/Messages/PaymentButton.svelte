<script lang="ts">
	export let label = 'Charge Your Account.';
	export let webPaymentUrl = 'https://www.aibrary.dev/chat/payment?chat=true';
	let loading = false;

	async function initiatePayment() {
		try {
			loading = true;

			if ('getDigitalGoodsService' in window) {

				// Fetch SKU details
				const service = await window.getDigitalGoodsService('https://play.google.com/billing');

				const skuDetails = await service.getDetails(['credit_5usd']);

				const item = skuDetails[0];

				// Format localized price (optional, for logging or showing in UI)
				const localizedPrice = new Intl.NumberFormat(
					navigator.language,
					{ style: 'currency', currency: item.price.currency }
				).format(item.price.value);

				// Define payment methods
				const paymentMethods = [{
					supportedMethods: 'https://play.google.com/billing',
					data: { sku: item.itemId }
				}];

			
				const paymentDetails = {
					total: {
						label: `Total`,
						amount: {currency: `USD`, value: `0`}
					}
				};

				const request = new PaymentRequest(paymentMethods, paymentDetails);
				console.log(request);
				

				const paymentResponse = await request.show();
				console.log(paymentResponse);
				const {purchaseToken} = paymentResponse.details;
				const response = await fetch('https://api.aibrary.dev/v0/payment/logger', {
					method: 'POST',
					headers: {
					'Content-Type': 'application/json',
					'Accept': 'application/json'
					},
					body: JSON.stringify({
					productId:item.itemId,
					purchaseToken,
					})
				});
				const result = await response.json();
				console.log('Server response:', result);

				// if (await acknowledgePurchaseOnBackend(purchaseToken, sku)) {
				// 	// Optional: consume the purchase, allowing the user to purchase
				// 	// the same item again.
				// 	service.consume(purchaseToken);

				// 	// Optional: tell the PaymentRequest API the validation was
				// 	// successful. The user-agent may show a "payment successful"
				// 	// message to the user.
				// 	const paymentComplete =
				// 			await paymentResponse.complete('success');
				// } 
					

				
				// await paymentResponse.complete('success');


				// alert('Payment successful via Google Play! 🎉');
				location.reload();
			} else {
				// Fallback
				window.location.href = webPaymentUrl;
			}
		} catch (err) {
			console.error('Payment failed:', err);
			alert('Payment failed. Please try again.');
		} finally {
			loading = false;
		}
	}
</script>

<button
	on:click={initiatePayment}
	class="text-blue-600 dark:text-blue-400 underline hover:text-blue-800 dark:hover:text-blue-500 text-sm text-left disabled:opacity-50"
	disabled={loading}
>
	{#if loading}
		Loading...
	{:else}
		{label}
	{/if}
</button>
