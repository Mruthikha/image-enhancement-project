% Step 1: Read and preprocess the X-Ray image
xray_img = imread('shoulder_xray.jpg');
if size(xray_img, 3) == 3
    xray_img = rgb2gray(xray_img); % Convert to grayscale if RGB
end

% Step 2: Noise reduction using bilateral filter
noise_reduced_img = imbilatfilt(xray_img);

% Step 3: Contrast enhancement using CLAHE
contrast_enhanced_img = adapthisteq(noise_reduced_img);

% Step 4: Edge detection using Canny
edges = edge(contrast_enhanced_img, 'Canny');

% Step 5: Create an edge mask and blend with the original
edge_mask = imdilate(edges, strel('disk', 1)); % Dilate edges for visibility
masked_img = uint8(edge_mask) .* contrast_enhanced_img;

% Step 6: Combine the original image with enhanced features
fused_img = imfuse(contrast_enhanced_img, masked_img, 'blend');

% Step 7: Sharpen the final image
sharpened_img = imsharpen(fused_img, 'Radius', 2, 'Amount', 1.5);

% Step 8: Display results
figure;
subplot(2, 3, 1); imshow(xray_img); title('Original X-Ray');
subplot(2, 3, 2); imshow(noise_reduced_img); title('Noise Reduced');
subplot(2, 3, 3); imshow(contrast_enhanced_img); title('Contrast Enhanced');
subplot(2, 3, 4); imshow(edges); title('Edges Detected');
subplot(2, 3, 5); imshow(masked_img); title('Masked Image');
subplot(2, 3, 6); imshow(sharpened_img); title('Final Enhanced Image');

% Save the final enhanced image
imwrite(sharpened_img, 'enhanced_xray_with_edges.jpg');
